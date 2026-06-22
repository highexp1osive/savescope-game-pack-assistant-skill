# Contributor Checklist

Use this checklist before saying a Game Pack is ready for maintainer review.

## Required Files

- `savescope.game.json`
- `README.md`
- `parser/index.mjs`
- `adapter/index.mjs`
- `samples/parser-output.example.json`
- `data/` when the pack needs catalog, mapping, or guide data
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
- Save mapping data needs parser output, fixture, or maintainer-reviewed evidence before it can support `supported`.
- Guide/wiki summaries are reference data only unless separate parser evidence proves current-save state.
- Single-save observation never proves `supported`.
- Half-verifiable collections should stay `catalog`, not partial completion.

## Data Rules

- Put game content directories in `data/catalog`.
- Put save fields, flags, offsets, and enum mappings in `data/mappings`.
- Put route, quest, NPC, and wiki-derived structured summaries in `data/guides`.
- Record source and authorization for catalog and guide data.
- Do not copy long wiki text, private exports, or license-unclear source dumps.

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
