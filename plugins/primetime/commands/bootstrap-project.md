---
description: Starter et nyt projekt rigtigt op. Interviewer udvikleren (antager aldrig), og skriver derefter projektets fundament — Project Charter, første PRD, risici, valgt dokumentations-tier og docs-struktur. Skriver ALDRIG applikationskode.
argument-hint: "[valgfrit: projektnavn]"
allowed-tools: Read, Grep, Glob, Write, Bash
disable-model-invocation: true
---

# /bootstrap-project — læg fundamentet for et nyt projekt

Du hjælper en Primetime-udvikler med at starte et nyt projekt korrekt op, FØR der skrives kode. Din opgave er at forstå intentionen præcist og skrive projektets fundament-dokumenter. Du arbejder i to faser: **interview** og derefter **skriv fundamentet**. Skriv ALDRIG applikationskode i denne command.

Projektnavn fra argument: `$1` (spørg, hvis det mangler).

---

## FASE 1 — Interview (antag ALDRIG — spørg)

Dette er hele pointen med commanden: et projekt bygget på skjulte antagelser bliver til teknisk gæld. Så du **gætter ikke** manglende svar — du spørger. Stil spørgsmålene i små grupper (ikke 20 på én gang), og opsummer det, du har forstået, undervejs.

Undersøg først, om noget allerede findes (`README`, eksisterende `docs/`, kode, et tilbud/brief i repo'et) — så du ikke spørger om det, der allerede er svaret.

Afdæk mindst:

1. **Projekttype — internt eller kunde?** Er det et internt Primetime-værktøj eller et projekt for en ekstern kunde? (Det afgør governance-niveau, dataansvar og overdragelse. Kundeprojekter er som udgangspunkt Tier 2.)
2. **Formål og problem.** Hvorfor skal systemet eksistere? Hvilket konkret problem løser det, for hvem?
3. **Ejer/opdragsgiver og brugere.** Hvem har bestilt det (intern afdeling eller kunde), hvem godkender scope, og hvem er de faktiske brugere?
4. **Succes.** Hvordan måler vi, at det virker? (Konkret, ikke "det skal være godt".)
5. **Ude af scope.** Hvad skal systemet udtrykkeligt IKKE gøre — nu?
6. **Kritikalitet.** Hvad sker der, hvis systemet er utilgængeligt? (Afgør drifts- og backup-ambition.)
7. **Data og persondata.** Hvilke data håndteres? Er der persondata/GDPR? (Ved ja: marker at threat model og databehandling bliver påkrævet, ikke valgfrit.)
8. **Integrationer og identitet.** Skal det tale med eksterne systemer, logins (fx Entra/Outlook), API'er? AI-integration (RAG, Anthropic-API)?
9. **Stack og miljø.** Er sprog/framework besluttet, eller er det en åben beslutning? (Husk: lokal DB kører i Docker Desktop hos os.) Åbne stack-valg noteres som et ADR-behov, ikke som en antagelse.
10. **Tidsramme og ambition.** MVP nu, eller et større system? (Bruges til at vælge tier.)

Hvis brugeren svarer "det ved jeg ikke endnu" på noget væsentligt: skriv det som et **åbent spørgsmål** i dokumenterne — opfind aldrig et svar for at gøre dokumentet "færdigt".

**Foreslå et dokumentations-tier** (fra plantegningens 3.4) og bekræft med brugeren:
- **Tier 0** — script/utility, ingen data, ingen rigtige brugere → kun README + CLAUDE.md.
- **Tier 1** — internt værktøj, rigtige brugere, lav risiko → Charter + let PRD + current-state; ADR kun ved dyre valg.
- **Tier 2** — rigtigt system (AI-integration, persondata, integrationer, kundeprojekt) → fuld pakke inkl. arkitektur, ADR'er, threat model.

**STOP og opsummer** intention + valgt tier, og få et "ja" før du skriver filer.

---

## FASE 2 — Skriv fundamentet

Opret `docs/`-strukturen og skriv dokumenterne, der passer til tier'et. Skriv kun det, tier'et kræver — overdokumentation er også gæld. Brug `<UDFYLD: …>` for ægte ukendte; opfind intet.

Opret mappen først (fx `mkdir -p docs/product docs/architecture docs/adr docs/operations`), og skriv så:

### `docs/project-charter.md` (alle tiers ≥ 1) — maks. 1-2 sider
Systemets stabile intention. Felter: Projekttype (intern/kunde) · Tier · Ejer/opdragsgiver · Brugere · Hvorfor systemet eksisterer · Problemet det løser · Sådan måles succes · Udtrykkeligt ude af scope · Hvad sker der ved utilgængelighed. Tilføj metadata øverst: Owner, Status, Last verified (dato), Review trigger.

### `docs/product/prd-0001-<slug>.md` (Tier ≥ 1) — PRD som versioneret hypotese
Beskriv produktbehov, primære workflows, krav, **testbare acceptance criteria**, edge cases og målepunkter. Skriv eksplicit øverst, at PRD'en er en **hypotese**, som brugeradfærd, drift og feedback løbende korrigerer — ikke en uforanderlig sandhed. Marker usikre krav som antagelser, der skal valideres.

### `docs/current-state.md` (Tier ≥ 1)
Kort, verificeret status: i produktion (endnu intet), work in progress, næste godkendte arbejde, kendte problemer/risici, dato for seneste kontrol. Et navigationspunkt — ikke en parallel sandhed ved siden af kode og commits.

### Risici (indlejret i charter eller `docs/operations/risks.md`)
De vigtigste risici du hørte i interviewet, hver med: konsekvens, sandsynlighed, anbefalet handling.

### For Tier 2, tilføj desuden skeletter (ikke udfyldt indhold, men rammen + åbne spørgsmål):
- `docs/architecture/architecture-overview.md` — system context, komponenter, data flows, trust boundaries, deploymentmodel.
- `docs/operations/threat-model.md` — hvis persondata/eksterne integrationer/identitet indgår.
- Noter **ADR-behov**: enhver beslutning, du hørte, som er dyr at omgøre (stack, datamodel, auth, ekstern kontrakt) → nævn at der skal køres `/create-adr` på den. Beslut den ikke selv her.

### Til sidst
- Hvis der ikke findes en `CLAUDE.md`, så foreslå at oprette en fra Primetime-skabelonen (peg på standarderne, stopkriterier, kommandoer) — men skriv den kun, hvis brugeren siger ja.
- Skriv en kort besked: hvilke filer du oprettede, hvilke `<UDFYLD>`/åbne spørgsmål der mangler svar, hvilket tier der blev valgt, og hvad det naturlige næste skridt er (typisk `/create-adr` på et åbent arkitekturvalg, derefter `/plan-feature` på første feature).

---

## Guardrails
- **Ingen applikationskode.** Denne command rører kun `docs/`, `README.md` og (efter ja) `CLAUDE.md`. Ikke `src/`, ikke tests, ikke config.
- **Antag aldrig et uklart svar.** Er noget væsentligt uafklaret: spørg, eller skriv det som et åbent spørgsmål. Et pænt udfyldt felt bygget på et gæt er værre end et ærligt `<UDFYLD>`.
- **PRD er en hypotese, ikke en kontrakt.** Skriv den, så virkeligheden må rette den.
- **Persondata / auth / ny ekstern kontrakt** → marker som stopkriterier og som noget, der kræver ADR og evt. threat model — gør det ikke bare.
