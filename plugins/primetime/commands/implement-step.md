---
description: Implementerer ÉT godkendt trin (slice) fra en plan i docs/plans/, med tests og en ærlig selv-rapport. Arbejder i små reversible ændringer, følger kodestandarden, og stopper ved afvigelser fra planen eller ved et stopkriterium.
argument-hint: "<plan-slug> [slice-nummer]"
allowed-tools: Read, Grep, Glob, Edit, Write, Bash
disable-model-invocation: true
---

# /implement-step — byg ét godkendt trin

Du implementerer PRÆCIS ét trin (én slice) fra en godkendt plan. Ikke mere. Du er den eneste command, der rører applikationskode, og derfor den, hvor disciplinen betyder mest.

Argument: `$1` = plan-slug (fil i `docs/plans/`), `$2` = valgfrit slice-nummer. Mangler slice-nummeret, så tag det næste ikke-færdige slice i planen og bekræft med brugeren, hvilket du er ved at bygge.

---

## Sådan arbejder du

1. **Læs grundlaget.** Planen i `docs/plans/<slug>.md`, `CLAUDE.md`, kodestandarden (kommentarer/navngivning), de relevante sti-specifikke regler i `.claude/rules/`, og den eksisterende kode, du skal ændre. Er planen uklar om dette slice, så STOP og spørg — du implementerer ikke et gæt.

2. **Bekræft scope før du skriver.** Sig kort: hvilket slice, hvilke filer du forventer at røre, og hvad "færdigt" betyder (acceptance criteria for dette slice). Rører slicet noget, der ikke står i planen, er det en afvigelse → stop (se guardrails).

3. **Implementér i en lille, sammenhængende ændring.** Kun dette slice. Følg kodestandarden. Foretræk den simpleste løsning, der opfylder kravet; introducér kun en abstraktion, hvis planen eller et af plantegningens kriterier kræver det. Læs altid en fil, før du ændrer den — gæt aldrig dens indhold.

4. **Skriv tests sammen med koden.** Dæk acceptance criteria og de vigtige edge cases/fejltilstande. For LLM-/AI-dele: evals og test af den deterministiske skal (retrieval, parsing, tool-kald, timeout/fejl-fallback), ikke `assertEqual` på et modelsvar.

5. **Kør valideringen lokalt.** Formatter, linter, type-/compile-check og de relevante tests (brug projektets kommandoer fra `CLAUDE.md`). Retter du fejl, så hold dig stadig inden for dette slice.

6. **Selv-rapport (obligatorisk).** Afslut med en ærlig rapport:
   - Hvad blev ændret (filer/moduler) og hvorfor.
   - **Antagelser** du traf, og **afvigelser** fra planen (hvis nogen).
   - Hvilke tests du skrev, hvad der kørte grønt, og **hvad du IKKE testede**.
   - Åbne risici og eventuelle opfølgninger.
   - Forslag til commit-besked i Conventional Commits-format (fx `feat: ...`). Du må gerne committe på en feature-branch, men **du merger ikke** — det kræver review.

7. **Peg videre.** Mind om, at slicet skal gennem `/review-change`, før det merges, og hvad det næste slice er.

---

## Guardrails — stop og bed om godkendelse, hvis…
- Ændringen ville røre **auth/adgang, persondata, en ny service/database/dependency/framework, en offentlig API-kontrakt, eller en irreversibel migration/sletning.**
- Du opdager, at planen er **forkert eller mangelfuld** for dette slice (afvigelse) — implementér ikke udenom; stop og sig det, så planen kan rettes (evt. via `/plan-feature` eller en ADR).
- Du bliver fristet til at **deaktivere en test eller en kontrol** for at komme videre.
- Kravet er **uklart, eller flere rimelige løsninger er mulige.**

I alle disse tilfælde: analysér gerne og foreslå, men **udfør ikke** uden et menneskeligt ja.

## Faste regler
- **Ét slice ad gangen.** Aldrig "mens jeg er i gang, tager jeg lige det næste også".
- **Små, reversible ændringer.** Skal kunne reviewes og rulles tilbage.
- **Ingen skjulte antagelser.** Alt usikkert skrives i selv-rapporten.
- **Dokumentation følger koden.** Rører ændringen noget, docs beskriver, så opdatér docs i samme ombæring.
