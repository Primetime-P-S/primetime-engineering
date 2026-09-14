---
description: Uafhængigt review af en ændring — krav, sikkerhed, kode og dokumentation. Delegerer til reviewer-subagenten for at få frisk kontekst, leder aktivt efter fejl trods grønne tests, og rapporterer før den evt. retter.
argument-hint: "[branch, commit-range eller sti — ellers de uncommittede ændringer]"
allowed-tools: Read, Grep, Glob, Bash, Task
disable-model-invocation: true
---

# /review-change — uafhængigt review

Du kører et uafhængigt review af en ændring, før den merges. Pointen er en *anden* vurdering end den, der skrev koden — så du fanger det, forfatteren (menneske eller AI) var blind for. Grønne tests er ikke bevis for korrekthed.

Omfang fra argument: `$1` (branch, commit-range eller sti). Mangler det, så review de uncommittede ændringer / den aktuelle branch mod `main`.

---

## Sådan gør du

1. **Afgræns ændringen.** Find diffen (fx `git diff`), og find den plan/PRD/ADR, ændringen hører til, så du kan reviewe mod det, der var *aftalt* — ikke kun mod koden i sig selv.

2. **Delegér til `reviewer`-subagenten.** Brug reviewer-subagenten (via Task) til selve gennemgangen. Den kører i sin egen kontekst — den er ikke "forurenet" af samtalen, hvor koden blev skrevet, og er derfor reelt mere uafhængig. Giv den diffen + links til plan/acceptance criteria.

3. **Bed den vurdere mindst:**
   - **Krav:** opfylder ændringen acceptance criteria — og kun dem (ingen scope-snig)?
   - **Korrekthed:** edge cases, fejltilstande, samtidighed, data-integritet. Forsøg aktivt at *falsificere*, at løsningen er korrekt.
   - **Sikkerhed:** input-validering, auth, secrets, injection (også prompt injection i AI-flows), persondata.
   - **Kode:** enkelhed vs. unødig abstraktion, kodestandard/kommentarer, filansvar, navngivning.
   - **Tests:** dækker de det rigtige, eller er de grønne af de forkerte grunde? Er noget vigtigt utestet?
   - **Dokumentation:** afspejler docs/ADR/current-state den faktiske ændring?

4. **Rangér fund** som `Critical`, `High`, `Medium`, `Low` eller `Suggestion`, hver med: hvad, hvor (fil/linje), hvorfor det er et problem, og forslag til udbedring.

5. **Rapportér FØRST.** Præsentér fundene for brugeren. Ret ikke noget i samme åndedrag.

---

## Guardrails
- **Rapport før rettelse.** Automatisk rettelse sker kun som en særskilt, afgrænset handling, som brugeren beder om bagefter — og et fund af typen Critical/High bør typisk tilbage gennem `/implement-step` med et menneskeligt ja, ikke lappes på stedet.
- **AI-review er ikke menneskelig godkendelse.** Du kan finde fejl, men du ejer ikke forretnings-, risiko- eller arkitekturbeslutningen. Som eneudvikler er dette dit bedste "andet sæt øjne" — men husk, at det ikke erstatter periodisk ægte eksternt review.
- **Vær konkret, ikke høflig-vag.** Et review, der siger "ser fint ud", er værdiløst. Find noget, eller forklar præcist, hvad du kontrollerede, og hvorfor det holder.
