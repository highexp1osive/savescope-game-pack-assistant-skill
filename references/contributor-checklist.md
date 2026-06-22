# Contributor Checklist

Use this checklist before saying a Game Pack is ready for maintainer review.

## Required Files

- `savescope.game.json`
- `README.md`
- `parser/index.mjs`
- `adapter/index.mjs`
- `samples/parser-output.example.json`
- `reports/detail.report.json`
- `reports/detail.report-data.json`
- `tests/fixtures/*.case.json`

## README Must State

- Supported game, platform, and game version.
- Current support scope.
- Known limitations.
- Data sources.
- Validation commands run.
- Safety statement: no real user saves, private exports, secrets, or unauthorized assets.
- Author of the Game Pack.

## Evidence Rules

- `fixture-tested` must come from a passing fixture runner.
- Adapter fixtures prove adapter mapping only.
- Parser fixtures prove save fixture -> parser output -> adapter -> report data.
- Catalog data never proves current-save state.
- Single-save observation never proves `supported`.
- Half-verifiable collections should stay `catalog`, not partial completion.

## Minimum Review Handoff

Run:

```bash
npm run validate:game-pack -- <game-pack-dir>
npm run test:game-pack -- <game-pack-dir> --mode=parser
npm run review:game-pack -- <game-pack-dir> --fixture-mode=parser
npm run assistant:game-pack -- check <game-pack-dir>
```

Handoff is acceptable when:

- Validation passes.
- Parser fixture mode has no failed cases.
- Review decision is not `blocked`.
- Assistant check has empty `summary.blockingStageIds`.
- Any remaining tasks are explicit maintainer-review or contributor-followup tasks.
