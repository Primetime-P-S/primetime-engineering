---
description: Vurderer om en beslutning kræver en ADR, og dokumenterer den — kontekst, reelle alternativer, trade-offs og konsekvenser. Sætter status til Proposed; AI må ikke acceptere sin egen ADR.
argument-hint: "<beslutningen der skal træffes, kort>"
allowed-tools: Read, Grep, Glob, Write
disable-model-invocation: true
---

# /create-adr — dokumentér en arkitekturbeslutning

Du hjælper en Primetime-udvikler med at afgøre, om en beslutning skal fastholdes som en Architecture Decision Record (ADR), og med at skrive den ordentligt. En ADR fanger *hvorfor* et valg blev truffet, så systemet kan overtages senere. Du beslutter ikke selv — du forbereder beslutningen og lader mennesket godkende.

Beslutning fra argument: `$1` (spørg, hvis den mangler).

---

## FASE 1 — Skal det overhovedet være en ADR?

Læs relevant kontekst (`docs/adr/` for tidligere ADR'er og næste nummer, `docs/architecture/`, relevant PRD/plan, berørt kode). Vurdér så mod kriterierne: opret en ADR, når beslutningen

- er **dyr at omgøre**, eller
- **påvirker flere områder**, eller
- ændrer **sikkerhed eller dataejerskab**, eller
- introducerer **væsentlig teknologi** (framework, database, ekstern service), eller
- ændrer en **ekstern kontrakt/API**, eller
- har **flere realistiske alternativer**.

Rammer den ingen af kriterierne: sig det ærligt — så er en ADR overdokumentation, og en note i planen/koden er nok. Er du i tvivl, så spørg brugeren frem for at antage.

---

## FASE 2 — Skriv ADR'en

Skriv `docs/adr/ADR-<NNNN>-<slug>.md` (fortløbende nummer, fx `ADR-0007-...`). Brug præcis disse felter:

- **Metadata:** Owner, dato, Last verified, links (Related nedenfor).
- **Status:** `Proposed` — ALTID Proposed når du skriver den. Du må ikke selv sætte den til Accepted. Kun mennesket accepterer.
- **Context:** Problemet, begrænsningen og det beslutningsrum, der reelt skal træffes valg i. Nøgternt og konkret.
- **Decision drivers:** De vigtigste krav og hensyn, valget skal balancere.
- **Considered options:** De **reelle** alternativer — mindst to, gerne tre — hver med fordele og ulemper. Ingen stråmænd opstillet kun for at få det ønskede valg til at vinde. Hvis der reelt kun er ét alternativ, er det måske ikke en ADR.
- **Decision:** Den anbefalede retning — men markeret som *forslag til godkendelse*, ikke som truffet.
- **Consequences:** Både positive og negative konsekvenser, inkl. hvad valget gør sværere eller dyrere senere.
- **Validation:** Hvordan og hvornår beslutningen senere revurderes (hvilket signal ville få os til at omgøre den).
- **Related:** Links til PRD, plan, issues, pull requests og tidligere/afløste ADR'er.

---

## Til sidst
Skriv en kort besked: ADR'ens nummer og sti, at status er `Proposed` og afventer din accept, og en neutral opsummering af trade-off'et mellem alternativerne — uden at presse dit foretrukne valg. Nævn, at du selv ændrer status til `Accepted` (eller `Rejected`), når du har taget stilling, og at en senere afløsning markeres `Superseded` med link til den nye ADR.

## Guardrails
- **AI accepterer ikke sin egen ADR.** Status forbliver `Proposed`, til mennesket beslutter.
- **Reelle alternativer, ingen stråmænd.** Hvis du kun kan finde ét seriøst alternativ, så sig det — måske er en ADR unødvendig.
- **Ingen kode.** Denne command dokumenterer en beslutning; den implementerer den ikke.
- **Nøgtern tone.** Præsentér trade-offs afbalanceret, så en fremtidig læser kan forstå — ikke kun konklusionen, men hvorfor.
