# Installer Primetime-plugin'et (guide til nye brugere)

Denne guide er til dig, der skal have `primetime`-pluginet ned på din egen maskine — ikke til dig, der vedligeholder marketplace-repoet (se i så fald [`plugin-setup-guide.md`](./plugin-setup-guide.md)).

Repoet er **offentligt**, så der kræves ingen GitHub-login eller adgangstoken for at installere pluginet.

---

## Forudsætninger

- [Claude Code](https://claude.com/claude-code) installeret — enten som CLI eller som VS Code-extension.
- Ingen GitHub-adgang nødvendig (repoet er offentligt).

## 1. Åbn Claude Code

- **VS Code:** åbn Claude Code-panelet (extension-ikonet i sidebjælken, eller `Ctrl+Esc`).
- **Terminal:** skriv `claude` i en mappe.

> **Vigtigt:** `/plugin ...` er slash-kommandoer **inde i Claude Code** — ikke almindelige PowerShell/terminal-kommandoer. Skriver du dem direkte i PowerShell, får du `The term '/plugin' is not recognized`.

## 2. Tilføj marketplace'et og installer plugin'et

Skriv følgende ved Claude-promptet (ét ad gangen, tryk Enter):

```
/plugin marketplace add Primetime-P-S/primetime-engineering
```

```
/plugin install primetime@primetime-engineering
```

Du bliver bedt om at vælge **scope** — vælg **`user`** (dig, alle projekter), så pluginet følger dig overalt, uden du skal geninstallere det per repo.

## 3. Bekræft at det virker

```
/plugin
```

Under **Installed** skal `primetime` nu stå som aktiv. Prøv fx:

```
/primetime:bootstrap-project
```

Du har nu adgang til:

| Kommando | Gør |
|---|---|
| `/primetime:bootstrap-project` | Interviewer og skriver projektets fundament |
| `/primetime:plan-feature` | Discovery + plan i små slices |
| `/primetime:create-adr` | Vurderer og dokumenterer en arkitekturbeslutning |
| `/primetime:implement-step` | Bygger ét godkendt slice med tests + selv-rapport |
| `/primetime:review-change` | Uafhængigt review (via reviewer-agenten) |
| `/primetime:azure-deploy` | Guided Azure deploy-runbook |

Samt agenterne `@primetime:planner` og `@primetime:reviewer`.

## 4. Hold pluginet opdateret

Når der kommer en ny version:

```
/plugin marketplace update primetime-engineering
/reload-plugins
```

---

## Fejlfinding

**`EBUSY: resource busy or locked, rmdir '...\.claude\plugins\marketplaces\...'`**
Rester fra et tidligere, afbrudt install-forsøg blokerer mappen. Slet den og prøv igen:
```powershell
Remove-Item -Path "$env:USERPROFILE\.claude\plugins\marketplaces\Primetime-P-S-primetime-engineering" -Recurse -Force
```
Kør så `/plugin marketplace add ...` igen inde i Claude Code.

**`Cannot add marketplace "primetime-engineering": its network source differs from the one declared...`**
Navnet `primetime-engineering` er allerede registreret til en anden kilde (fx en lokal sti fra et tidligere test-forsøg). Tjek `extraKnownMarketplaces` i `~/.claude/settings.json` (og evt. i det åbne projekts `.claude/settings.json`), og ret eller fjern den modstridende deklaration.

**`fatal: Cannot prompt because user interactivity has been disabled` / `unable to get password from user`**
Claude Code kører git non-interaktivt og kan ikke vise et login-prompt. Da repoet er offentligt, bør dette ikke længere ske — sker det alligevel, er der typisk en forkert/forældet credential-cache for `github.com`. Ryd den, eller brug HTTPS i stedet for SSH:
```powershell
git config --global url."https://github.com/".insteadOf "git@github.com:"
```

**Ændringer aktiveres ikke med det samme**
Kør `/reload-plugins`.

---

## Til hele teamet automatisk (valgfrit, avanceret)

Fremfor at hver udvikler selv kører `/plugin marketplace add`, kan I committe dette til et projekts `.claude/settings.json`, så marketplace og plugin auto-aktiveres, når nogen åbner projektet i Claude Code:

```json
{
  "extraKnownMarketplaces": {
    "primetime-engineering": {
      "source": { "source": "github", "repo": "Primetime-P-S/primetime-engineering" }
    }
  },
  "enabledPlugins": {
    "primetime@primetime-engineering": "project"
  }
}
```
