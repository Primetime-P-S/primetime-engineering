# Opsætning af Primetime-plugin'et i Claude Code

En praktisk guide til at samle commands og agenter til ét installérbart Claude Code-plugin, distribueret via et marketplace, så du (og senere et team) altid har metoden ved hånden.

Jeg har allerede samlet en færdig repo-skabelon til dig (i zip'en `primetime-engineering.zip`). Denne guide forklarer, hvad der er i den, hvordan du får den online, og hvordan du installerer og vedligeholder den.

---

## 0. Den mentale model (læs denne først)

Det vigtigste at forstå — for det afgør, hvad der lægges hvor:

> **Plugin'et giver udsagnsordene. Projektet giver konteksten.**

- **Plugin'et** (dette repo) rummer *commands* og *agenter* — den fælles metode, der er ens i alle projekter. Det installeres én gang og er tilgængeligt overalt.
- **Projektet** rummer *sin egen kontekst*: `CLAUDE.md`, `.claude/rules/`, `docs/`, hooks og CI. Et plugin **kan ikke** injicere et projekts `CLAUDE.md` eller projekt-regler — og det er meningen. Det ville jo ikke give mening, at et generisk plugin bestemte netop dette projekts stopkriterier.

Så: `/primetime:bootstrap-project` kommer fra plugin'et; de dokumenter og den `CLAUDE.md`, den *skriver*, bliver committet i projektet. De to lag arbejder sammen.

---

## 1. Hvad er i skabelonen

```
primetime-engineering/                 ← git-repo: både marketplace OG plugin-vært
├── .claude-plugin/
│   └── marketplace.json               ← marketplace-manifest (så /plugin kan finde plugin'et)
├── plugins/
│   └── primetime/                     ← selve plugin'et
│       ├── .claude-plugin/
│       │   └── plugin.json            ← plugin-manifest (navn, version, forfatter)
│       ├── commands/
│       │   ├── bootstrap-project.md
│       │   ├── plan-feature.md
│       │   ├── create-adr.md
│       │   ├── implement-step.md
│       │   ├── review-change.md
│       │   └── azure-deploy.md
│       └── agents/
│           ├── planner.md
│           └── reviewer.md
├── standards/                         ← menneskelæsbare standarder (kildesandhed)
├── templates/                         ← charter, PRD, ADR, ... (fyldes ud senere)
├── starter/                           ← skabelon-projekt (fyldes ud senere)
└── README.md
```

To ting værd at bemærke om strukturen (det er de klassiske fælder):

- **Kun `plugin.json` ligger i `.claude-plugin/`.** `commands/`, `agents/` osv. ligger i plugin-roden ved siden af — ikke inde i `.claude-plugin/`.
- Repo'et er **både marketplace og plugin**. `marketplace.json` peger på plugin'et med en relativ sti (`"source": "./plugins/primetime"`). Det er det normale mønster for én organisation med ét plugin.

De to manifest-filer, jeg har udfyldt:

**`.claude-plugin/marketplace.json`**
```json
{
  "name": "primetime-engineering",
  "owner": { "name": "Primetime (Niclas Nielsen)", "email": "nni@primetime.dk" },
  "plugins": [
    { "name": "primetime", "source": "./plugins/primetime", "description": "..." }
  ]
}
```

**`plugins/primetime/.claude-plugin/plugin.json`**
```json
{
  "name": "primetime",
  "displayName": "Primetime AI-udviklingsstandard",
  "description": "...",
  "version": "0.1.0",
  "author": { "name": "Primetime (Niclas Nielsen)", "email": "nni@primetime.dk" }
}
```

`name`-feltet i `plugin.json` (`primetime`) bliver **prefix** på alle commands. Derfor hedder de `/primetime:bootstrap-project` osv. Vil du have et kortere dagligt prefix, så omdøb plugin'et (fx til `pt`) — så bliver det `/pt:bootstrap-project`.

---

## 2. Få repo'et online

```bash
# i den udpakkede mappe
cd primetime-engineering
git init
git add .
git commit -m "chore: initial Primetime engineering plugin + marketplace"

# opret et privat repo hos din org på GitHub og push
git remote add origin git@github.com:<din-org>/primetime-engineering.git
git branch -M main
git push -u origin main
```

Før du pusher, kan du validere plugin'et lokalt:

```bash
claude plugin validate ./plugins/primetime
```

---

## 3. Installér i Claude Code

> **Vigtigt: `/plugin ...` er slash-kommandoer INDE i Claude Code — ikke PowerShell/terminal-kommandoer.** Skriver du dem i PowerShell, får du "The term '/plugin' is not recognized". Kun `claude plugin validate` er en rigtig terminal-kommando. Start derfor Claude Code først (`claude` i repo-mappen), og skriv så slash-kommandoerne ved Claude-prompten.

**Test lokalt uden GitHub (anbefalet først):** repo'et behøver ikke være pushet for at teste. Start Claude Code i mappen og tilføj marketplace'et fra den lokale sti:

```
claude                                   # i PowerShell, i repo-mappen → åbner Claude Code
```
```
/plugin marketplace add .                # HER, inde i Claude Code (ikke PowerShell)
/plugin install primetime@primetime-engineering
```

Virker `.` ikke, så brug den eksplicitte sti: `/plugin marketplace add ./.claude-plugin/marketplace.json`.

**Fra GitHub (når repo'et er pushet):**

```
/plugin marketplace add <din-org>/primetime-engineering
/plugin install primetime@primetime-engineering
```

- `/plugin` alene åbner en menu (Discover / Installed / Marketplaces …), hvor du kan se og styre det installerede.
- Ved install vælger du **scope**: `user` (dig, alle projekter), `project` (committes til dette projekt) eller `local` (dig, kun dette projekt). For dine egne generiske commands er `user` det rigtige — så har du dem overalt.
- Skift ikke aktiveres med det samme? Kør `/reload-plugins`.

Derefter har du:

| Kommando | Gør |
|---|---|
| `/primetime:bootstrap-project` | Interviewer og skriver projektets fundament |
| `/primetime:plan-feature` | Discovery + plan i små slices |
| `/primetime:create-adr` | Vurderer og dokumenterer en arkitekturbeslutning |
| `/primetime:implement-step` | Bygger ét godkendt slice med tests + selv-rapport |
| `/primetime:review-change` | Uafhængigt review (via reviewer-agenten) |
| `/primetime:azure-deploy` | Guided Azure deploy-runbook |

Og agenterne `@primetime:planner` og `@primetime:reviewer`.

---

## 4. Hvad der stadig sættes op pr. projekt

Plugin'et giver metoden; hvert nyt projekt skal stadig have sin kontekst. Den letteste vej: kør `/primetime:bootstrap-project` i et nyt repo — den skriver Charter, PRD og docs-strukturen. Derudover committer du i projektet:

- **`CLAUDE.md`** — projektets AI-indgang (kort; peger på standarderne + stopkriterier). Skabelon i plantegningen, afsnit 6.
- **`.claude/rules/`** — sti-specifikke regler, fx `tests.md` (jf. plantegningen 3.5).
- **`.claude/settings.json`** — hooks (formatter/secret-guard) + permissions.
- **`docs/`** — charter, PRD, ADR'er, current-state.

Dette committes, fordi det skal **følge med koden** og overleve overdragelse — i modsætning til plugin'et, som er et værktøjslag ovenpå.

**Avanceret (valgfrit):** vil du have, at fx kodekommentar-standarden altid er tilgængelig uden at kopiere den ind i hvert projekt, kan du lægge den i plugin'et (fx `plugins/primetime/standards/`) og lade en command referere den via `${CLAUDE_PLUGIN_ROOT}/standards/code-style-and-comments.md`. Så rejser den generiske standard med plugin'et, mens det projekt-specifikke bliver i projektet.

---

## 5. Opdatér plugin'et senere

Når du forbedrer en command:

```bash
# i primetime-engineering
# ret filen, hæv "version" i plugin.json (fx 0.1.0 → 0.2.0)
git commit -am "feat(plan-feature): tydeligere discovery-trin"
git push
```

Hos brugeren:

```
/plugin marketplace update primetime-engineering
/reload-plugins
```

Versionér plugin'et ligesom kode (semver). Udelader du `version`, falder Claude Code tilbage på git-tag/commit.

---

## 6. Når I bliver flere (team-distribution)

Du behøver ikke gøre noget nu, men sådan skaleres det: i et projekts `.claude/settings.json` (committet) kan du gøre marketplace'et kendt for alle, der åbner projektet:

```json
{
  "extraKnownMarketplaces": {
    "primetime-engineering": {
      "source": { "source": "github", "repo": "<din-org>/primetime-engineering" }
    }
  },
  "enabledPlugins": {
    "primetime@primetime-engineering": "project"
  }
}
```

Så får en ny kollega automatisk marketplace'et kendt, når de stoler på projektmappen. På Team/Enterprise kan plugins desuden håndhæves org-bredt via managed settings — det ser vi på, når det bliver relevant.

---

## Tjekliste

- ☐ Pak `primetime-engineering.zip` ud.
- ☐ (Valgfrit) omdøb plugin'et, hvis du vil have et kortere prefix end `/primetime:`.
- ☐ `git init` → commit → push til et privat org-repo.
- ☐ `claude plugin validate ./plugins/primetime`.
- ☐ `/plugin marketplace add <din-org>/primetime-engineering`.
- ☐ `/plugin install primetime@primetime-engineering` (scope: user).
- ☐ Test `/primetime:bootstrap-project` på et lille repo.
- ☐ Fyld `standards/`, `templates/` og `starter/` ud efterhånden (handlingsplanen, plantegningens afsnit 9).