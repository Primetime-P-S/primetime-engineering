# Bedste praksis: hvornår bruger jeg hvilken command?

En praktisk køreplan for `/primetime:*`-commanderne. Tommelfingerregel: **de følger den rækkefølge, arbejdet naturligt sker i** — fra "jeg har en idé" til "det er merget." Du springer sjældent et led over, og du kører næsten aldrig to trin i samme åndedrag.

```
NYT PROJEKT
   │
   ▼
/primetime:bootstrap-project    ← én gang, ved projektstart
   │
   ▼
┌─────────────────────────────────────────────┐
│  PR FEATURE / OPGAVE (gentages igen og igen) │
│                                               │
│   /primetime:plan-feature                    │
│         │                                    │
│         ▼ (kun hvis planen rejser et         │
│           arkitekturvalg)                    │
│   /primetime:create-adr                      │
│         │                                    │
│         ▼                                    │
│   /primetime:implement-step  (× N slices)    │
│         │                                    │
│         ▼                                    │
│   /primetime:review-change                   │
│         │                                    │
│         ▼                                    │
│   merge → næste feature                      │
└─────────────────────────────────────────────┘
   │
   ▼ (ad hoc, når du skal i produktion)
/primetime:azure-deploy
```

---

## 1. `/primetime:bootstrap-project` — **én gang, ved start**

**Brug den:** Dagen du opretter et nyt repo — før du har skrevet én linje kode. Også hvis et ældre projekt aldrig fik lavet ordentlig dokumentation ("gæld-oprydning") — så kører du den for at få Charter/PRD på plads bagudrettet.

**Brug den ikke:** Midt i et projekt til en ny feature — det er `/plan-feature`'s job. Bootstrap er projektets *fundament*, ikke dets løbende arbejde.

**Tommelfingerregel:** Kører du den mere end 1-2 gange pr. projekt, bruger du den forkert.

---

## 2. `/primetime:plan-feature` — **for hver ny feature/opgave, før du koder**

**Brug den:** Hver gang du skal bygge noget nyt — en feature, en bugfix af en vis størrelse, en ændring der rører mere end én-to linjer. Kør den, **før** du rører kode, selv når opgaven virker simpel — det er billigere at opdage en uklarhed her end tre commits inde.

**Brug den ikke:** Til triviel oprydning (rette en typo, opdatere en kommentar, en et-linjes rettelse uden adfærdsændring). Der er ingen fast grænse — brug din dømmekraft: **rører ændringen adfærd, data eller en beslutning, kør planen; er det ren kosmetik, spring den over.**

**Output:** `docs/plans/<slug>.md` — det dokument, `/implement-step` senere arbejder ud fra.

---

## 3. `/primetime:create-adr` — **kun når planen rejser et rigtigt valg**

**Brug den:** Når `/plan-feature` (eller du selv undervejs) støder på en beslutning, der er dyr at omgøre, påvirker flere områder, indfører ny teknologi, ændrer sikkerhed/dataejerskab, eller har flere reelle alternativer. Typiske triggere: "skal vi bruge Redis eller in-memory cache?", "hvilken auth-model?", "Postgres eller Cosmos DB?".

**Brug den ikke:** Til alt. De fleste features kræver **ingen** ADR — det er bevidst. Kører du den for hver lille detalje, mister ADR'erne deres værdi som "her var noget vigtigt at forstå senere." Kommanden siger selv fra, hvis den vurderer, at spørgsmålet ikke er ADR-værdigt.

**Rækkefølge:** Kan komme **før** `/implement-step` (afklar arkitekturen, før du bygger) — men kan også opstå **midt i** en implementering, hvis `/implement-step` selv støder på et uforudset valg og stopper for at spørge.

---

## 4. `/primetime:implement-step` — **for hvert slice i planen, ét ad gangen**

**Brug den:** Efter planen (og evt. ADR) er godkendt. Kør den én gang **per slice** — ikke én gang for hele featuren. Har planen 4 slices, kører du kommandoen 4 gange, og du reviewer/committer typisk imellem hver.

**Brug den ikke:** Uden en plan at arbejde fra. Har du ikke kørt `/plan-feature` først, har `/implement-step` intet at eksekvere — den er bevidst bygget til at læse en `docs/plans/`-fil, ikke til at "bare kode noget."

**Vigtigt mønster:** Stopper den midtvejs (fordi noget kræver en beslutning), er det ikke en fejl i workflowet — det er meningen. Tag stilling, og kør den igen på samme slice, eller send den tilbage til `/plan-feature`/`/create-adr`, hvis planen selv var forkert.

---

## 5. `/primetime:review-change` — **efter hvert slice er implementeret, før merge**

**Brug den:** Så snart et slice er færdigt og testet — **før** du merger til `main`. Ideelt kører du den efter hvert enkelt slice (ikke kun én gang til allersidst for hele featuren), så fejl fanges tæt på, hvor de opstod.

**Brug den ikke:** Som erstatning for at læse rapporten selv. Den er dit bedste "andet sæt øjne" som eneudvikler, men husk plantegningens pointe: AI-review er ikke det samme som uafhængig menneskelig godkendelse — book stadig et periodisk eksternt review på de vigtige systemer.

**Praktisk vane:** Gør det til en fast refleks — "slice implementeret → kør review-change → så merge." Aldrig merge uden det trin, heller ikke når du har travlt (*især* ikke når du har travlt).

---

## 6. `/primetime:azure-deploy` — **ad hoc, når du rent faktisk skal i skyen**

**Brug den:** Ikke en del af feature-loopet ovenfor. Kør den, når du står over for at skulle sætte et projekt op i Azure første gang, tilføje en ny Azure-service til et eksisterende projekt, eller genopfriske dig selv på, hvordan et miljø hænger sammen. Den er projekt-uafhængig i den forstand, at du kan køre den igen og igen, efterhånden som projektets behov vokser (flere services, nyt miljø som `test`/`prod`).

**Brug den ikke:** Til at faktisk udføre deploymentet — den producerer en guide, du selv eksekverer. Og ikke i stedet for en ADR, hvis valget af Azure-service i sig selv er et arkitekturvalg værd at dokumentere (kør evt. `/create-adr` på selve valget først, `/azure-deploy` bagefter på hvordan det sættes op).

---

## Kort svar, hvis du glemmer alt andet

| Situation | Command |
|---|---|
| Nyt projekt starter | `/primetime:bootstrap-project` |
| Ny feature/opgave, før kode skrives | `/primetime:plan-feature` |
| Planen rejser et arkitekturvalg | `/primetime:create-adr` |
| Klar til at bygge ét godkendt trin | `/primetime:implement-step` |
| Et slice er færdigt, før merge | `/primetime:review-change` |
| Skal sættes op / udvides i Azure | `/primetime:azure-deploy` |

Og de to agenter, du sjældent kalder direkte, men som arbejder i baggrunden: **`planner`** bruges typisk *inde i* `/plan-feature`, når du vil have en dybere, isoleret analyse af repo'et først; **`reviewer`** er motoren bag `/review-change`.