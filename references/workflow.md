# SaveScope Game Pack Assistant Workflow

## Stage 0: Locate Or Create

If the Game Pack does not exist, create it from the SaveScope repository root:

```bash
npm run create:game-pack -- examples/game-packs/my-game --id my-game --name "My Game" --author "Contributor Name"
```

If it already exists, read:

- `savescope.game.json`
- `README.md`
- `reports/detail.report.json`
- `reports/detail.report-data.json`
- `tests/fixtures/*.case.json`

Then run:

```bash
npm run validate:game-pack -- examples/game-packs/my-game
```

Keep `status: "experimental"` unless maintainers explicitly approve an upgrade.

## Stage 1: Confirm Inputs

Ask for:

- Game name, platform, and game version.
- Public sample availability.
- Before/after samples and the exact known change in each pair.
- Public data sources for catalogs, names, routes, or NPC/item lists.
- Asset authorization.
- Known encryption, compression, checksum, anti-cheat, cloud-save, or platform constraints.

If input is thin, continue only as an experimental draft and keep features conservative.

## Stage 2: Inspect And Diff

Single save:

```bash
npm run inspect:save -- ./samples/SaveData.sav --game-id my-game --platform pc --known "角色名=Player:profile.displayName"
```

Before/after pair:

```bash
npm run diff:saves -- ./samples/before.sav ./samples/after.sav --game-id my-game --platform pc --known-change "等级=10>11:profile.level"
```

Rules:

- Candidate fields only become parser hypotheses.
- One before/after pair is not enough for full support.
- Too many changed regions usually means compression, encryption, timestamp churn, or checksum.
- Convert contributor tasks into short, concrete questions.

## Stage 3: Implement Conservatively

Recommended edit order:

1. `samples/parser-output.example.json`
2. `adapter/index.mjs`
3. `reports/detail.report-data.json`
4. `reports/detail.report.json`
5. `tests/fixtures/*.case.json`
6. `parser/index.mjs`
7. `README.md`

Run after small milestones:

```bash
npm run validate:game-pack -- examples/game-packs/my-game
```

Run after parser, adapter, or fixture changes:

```bash
npm run test:game-pack -- examples/game-packs/my-game --mode=parser
```

## Stage 4: Review Handoff

Run:

```bash
npm run review:game-pack -- examples/game-packs/my-game --fixture-mode=parser
npm run assistant:game-pack -- check examples/game-packs/my-game
```

Continue only if:

- Validation passes.
- Parser fixtures have no failed cases.
- Review is not blocked.
- Assistant check has no `blockingStageIds`.
- README explains support scope, limits, data sources, validation, safety, and author.

Passing these checks means "ready for maintainer review", not automatic publishing.
