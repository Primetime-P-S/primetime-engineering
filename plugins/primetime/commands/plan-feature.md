---
description: Omsætter et feature-behov til en gennemtænkt plan — scope, testbare acceptance criteria, teknisk plan i små vertikale slices og en testplan — FØR der skrives kode. Synliggør uklarheder og stopper ved dem. Skriver ikke applikationskode.
argument-hint: "<feature-navn eller kort beskrivelse af behovet>"
allowed-tools: Read, Grep, Glob, Write
disable-model-invocation: true
---

# /plan-feature — plan før implementation

Du hjælper en Primetime-udvikler med at planlægge en feature ordentligt, FØR nogen skriver kode. Resultatet er et plan-dokument, som `/implement-step` senere eksekverer ét trin ad gangen. Du arbejder i to faser: **discovery** og **plan**. Du skriver INGEN applikationskode i denne command.

Behov fra argument: `$1`. Er det tomt eller vagt, så bed om en kort beskrivelse af, hvad featuren skal gøre og for hvem.

---

## FASE 1 — Discovery (find uklarhederne, antag dem ikke væk)

Målet her er at afdække alt det, en naiv implementering ville gætte forkert på. Ingen kode.

1. **Læs konteksten først.** `CLAUDE.md`, `docs/project-charter.md`, den relevante PRD, `docs/architecture/`, `docs/current-state.md`, eksisterende ADR'er, og den kode/de moduler, featuren rører. Forstå, hvordan systemet allerede hænger sammen, før du foreslår noget nyt.

2. **Afklar behovet mod PRD'en.** Hvilket produktbehov/hypotese tjener featuren? Hvis den ikke passer til noget i PRD'en, så sig det — måske skal PRD'en opdateres først.

3. **Find og LIST uklarhederne eksplicit.** Gennemgå aktivt:
   - Krav der kan tolkes på flere måder, eller hvor flere rimelige brugeroplevelser er mulige.
   - Edge cases: tomme tilstande, fejl, samtidighed, store mængder, rettigheder.
   - Afhængigheder: andre moduler, eksterne services, migrationer.
   - Persondata, auth/adgang, offentlige API-kontrakter, integrationer.
   - Ikke-funktionelle krav: performance, tilgængelighed, omkostning (særligt for LLM-/AI-features: token-forbrug, rate limits, timeout/fallback).

4. **STOP ved de væsentlige uklarheder og spørg.** Præsentér uklarhederne som en kort nummereret liste, og bed om afklaring på dem, der ændrer designet. Gæt ikke for at komme videre — en forkert antagelse her er dyrere end et spørgsmål. Mindre, uskadelige antagelser må du gerne træffe, men så SKAL de skrives synligt som "antagelse" i planen.

**Gå ikke videre til fase 2, før de designafgørende uklarheder er afklaret.**

---

## FASE 2 — Skriv planen

Skriv én fil: `docs/plans/<slug>.md` (opret `docs/plans/` hvis den mangler). Filen skal indeholde:

### 1. Feature-specifikation
Kort: hvad featuren gør, for hvem, og hvilket PRD-behov/hypotese den tjener. Hvad er udtrykkeligt ude af scope for denne feature.

### 2. Antagelser og åbne spørgsmål
De antagelser, du traf (synligt), og eventuelle resterende åbne spørgsmål. Aldrig skjulte antagelser.

### 3. Testbare acceptance criteria
Punktform, i "given/når/så"-stil hvor det passer. Hvert kriterium skal kunne verificeres objektivt — undgå "det skal føles hurtigt". Dæk også de vigtige edge cases og fejltilstande.

### 4. Teknisk plan — små vertikale slices
Bryd implementeringen op i **små, reversible slices**, der hver leverer noget sammenhængende på tværs af de nødvendige lag (fx frontend + backend + tests for ét delbehov). For hver slice:
- Hvad den omfatter, og hvilke filer/moduler den forventes at røre.
- Hvorfor den er skåret sådan (så `/implement-step` kan tage én ad gangen).
- Rækkefølge og afhængigheder mellem slices.
Foretræk den **simpleste løsning**, der opfylder de kendte krav. Introducér kun en abstraktion, hvis mindst ét af plantegningens kriterier er opfyldt (flere implementationer, isolering af ekstern afhængighed, tydelig domænegrænse, testbarhed, eller en accepteret ADR) — og skriv i så fald både gevinst og omkostning.

### 5. Testplan
Hvad testes på hvilket niveau (unit, integration, evt. e2e). For LLM-/AI-features: beskriv **evals** (fast input → forventede egenskaber) frem for `assertEqual` på et modelsvar, og test den deterministiske skal (retrieval, parsing, tool-kald, fejl/timeout-håndtering) hårdt.

### 6. ADR- og threat-model-behov
Vurdér, om featuren udløser en beslutning, der kræver en ADR (dyr at omgøre, påvirker flere områder, ny teknologi, ændrer sikkerhed/dataejerskab eller ekstern kontrakt, flere realistiske alternativer). Hvis ja: nævn det, og send brugeren til `/create-adr` — beslut den ikke selv her. Udløser featuren nye data flows/trust boundaries/persondata: nævn threat-model-behov.

### 7. Stopkriterier for implementeringen
List de punkter i denne feature, hvor `/implement-step` SKAL stoppe og bede om godkendelse (auth, persondata, ny service/dependency, irreversibel migration, afvigelse fra denne plan).

### Til sidst
Skriv en kort besked i chatten: hvor planen ligger, hvilke åbne spørgsmål der evt. mangler svar, om der er et ADR-behov at tage først, og at næste skridt — efter din godkendelse af planen — er `/implement-step` på slice 1.

---

## Guardrails
- **Ingen applikationskode.** Denne command producerer kun et plan-dokument. Implementering sker i `/implement-step`, efter du har godkendt planen.
- **Uklarhed → stop og spørg.** Designafgørende uklarheder afklares før planen skrives; mindre antagelser skrives synligt. Ingen skjulte gæt.
- **Små, reversible slices.** Ingen big-bang-plan. Hver slice skal kunne reviewes og rulles tilbage.
- **Enkelhed før abstraktion.** Ny kompleksitet skal begrundes mod plantegningens kriterier.
- **Planen er til godkendelse.** Den er et oplæg, mennesket godkender — ikke en ordre, AI'en selv effektuerer.
