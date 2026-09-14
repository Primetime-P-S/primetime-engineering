# primetime-engineering

Primetimes centrale repo for AI-assisteret softwareudvikling. Det er **kilden til sandhed** for vores metode og fungerer samtidig som et **Claude Code plugin-marketplace**.

## Hvad ligger her

- `plugins/primetime/` — selve plugin'et: de commands og agenter, der udgør metoden. Distribueres til Claude Code via marketplace'et.
- `.claude-plugin/marketplace.json` — marketplace-manifest, så plugin'et kan installeres med `/plugin`.
- `standards/` — de menneskelæsbare standarder (kodestil + kommentarer, AI-politik, sikkerhed, test, git/PR, AI-systemer, Definition of Done). Refereres fra hvert projekts `CLAUDE.md`.
- `templates/` — skabeloner: charter, PRD, ADR, architecture, threat model, current-state, PR-template.
- `starter/` — et køreklart skabelon-projekt, nye repos startes fra.

## Installer plugin'et i Claude Code

```
/plugin marketplace add <din-org>/primetime-engineering
/plugin install primetime@primetime-engineering
```

Herefter er commands tilgængelige som `/primetime:bootstrap-project`, `/primetime:plan-feature` osv., og agenterne som `@primetime:planner` og `@primetime:reviewer`.

## Vigtigt: plugin vs. projekt

Plugin'et giver **udsagnsordene** (commands + agenter) — det samme overalt. Hvert **projekt** giver **konteksten**: sin egen `CLAUDE.md`, sine `.claude/rules/`, sine `docs/` og sine hooks/CI. Se plantegningen for den fulde fordeling.

Se `docs/` i Claude-projektet "Fælles fundament for AI-assisteret softwareudvikling" for plantegningen og opsætningsguiden.
