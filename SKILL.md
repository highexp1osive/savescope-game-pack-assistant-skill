---
name: savescope-game-pack-assistant-skill
description: Use when helping create, update, inspect, test, review, or prepare a SaveScope Game Pack with SaveScope CLI tools, especially when converting save-file observations, parser output, report data, fixtures, and contributor evidence into a conservative game support package.
---

# SaveScope Game Pack Assistant

## Overview

Use this skill to guide a SaveScope Game Pack from early draft to maintainer review. Keep the workflow lightweight: use the SaveScope repository's existing CLI, schema, template, and review tools; do not invent a new DSL, MCP server, renderer, or component system.

Assume the working directory is a SaveScope app repository that contains `package.json` and the Game Pack scripts.

## Core Rules

- Treat a Game Pack as a complete game support package, not an arbitrary plugin.
- Keep new packs `experimental` until maintainers review them.
- The Game Pack author is the support-package/detail-page author, not the game developer.
- Never upgrade a feature to `supported` from a single save, a guessed offset, wiki/catalog data, or one unconfirmed before/after diff.
- Do not use `partial`; if only some items can be verified, keep the whole feature `catalog`, `planned`, or `unknown`.
- Keep Game Pack data split into `data/catalog`, `data/mappings`, and `data/guides`; do not mix catalogs, save mappings, and wiki/guide notes into one database file.
- Treat wiki/guide content as structured, sourced reference data only; do not copy long wiki text or use it as current-save evidence.
- Do not add React pages, CSS overrides, custom module types, or Core changes from a Game Pack.
- Do not commit real user saves, private exports, access keys, unauthorized images, or `tests/.artifacts/`.

## Workflow

1. Read the SaveScope repo guidance first: `AGENTS.md`, `PROJECT_MAP.md`, then `docs/standards/game-pack-standard.md`.
2. If the user is creating a new pack, run:

   ```bash
   npm run create:game-pack -- examples/game-packs/my-game --id my-game --name "My Game" --author "Contributor Name"
   ```

3. Collect the minimum facts: target game, platform, version, public sample availability, before/after samples, known changes, catalog sources, save-mapping evidence, guide/wiki authorization, asset authorization, and any encryption/compression/checksum constraints.
4. Use `inspect:save` for a single save and `diff:saves` for before/after evidence. Treat their candidates as parser hypotheses until confirmed.
5. Update the pack in this order: `samples/parser-output.example.json`, `adapter/index.mjs`, report data, report config, fixture cases, parser, README.
6. After each meaningful change, run `validate:game-pack`.
7. When parser, adapter, or fixtures change, run `test:game-pack -- --mode=parser` for the target pack.
8. Before handoff, run `review:game-pack -- --fixture-mode=parser` and `assistant:game-pack -- check`.
9. If any blocking stage, failed fixture, unclear evidence, or unsafe file appears, stop and ask the contributor for concrete material.

Read [workflow.md](references/workflow.md) for the full stage checklist.

## CLI Output

All Game Pack CLI output is JSON. Parse stdout/stderr instead of relying on free text.

- `status: "ok"` means wrapper command success; read `data` and `summary`.
- `status: "error"` means command failure; read `error.code`, `error.message`, `error.details`, and `summary.exitCode`.
- No `status` means the command uses its own tool schema; branch by `tool`.
- Prefer JSON `summary.exitCode` over the outer shell's displayed exit code.

Read [cli-output.md](references/cli-output.md) when handling failures or deciding whether to continue.

## Evidence Gate

Feature status must stay conservative:

| Status | Use when |
| --- | --- |
| `supported` | Parser output, fixture-tested evidence, or maintainer-approved manual evidence supports current-save state |
| `catalog` | The pack has reference/catalog/route data but cannot verify current-save state |
| `planned` | The feature is intended but not implemented |
| `unsupported` | The feature is explicitly not supported |
| `unknown` | The evidence is insufficient |

For contributor handoff and review readiness, use [contributor-checklist.md](references/contributor-checklist.md).

Data evidence must stay separated:

- `data/catalog` lists game content and never proves current-save state.
- `data/mappings` maps save fields, flags, offsets, or enums; it needs parser output, fixture, or maintainer-reviewed evidence before it can support `supported`.
- `data/guides` stores route, quest, NPC, or wiki-derived structured summaries; it is reference/catalog content and does not count toward completion.

## Stop Conditions

Stop automatic work and produce a contributor question when:

- There is only one save and the next step would claim `supported`.
- A before/after candidate lacks a second confirming sample.
- Save structure suggests encryption, compression, checksum, anti-cheat, or platform restrictions without a known handling path.
- Data source or asset authorization is unclear.
- Wiki/guide material has unclear source or redistribution rights.
- Save mapping data lacks parser output, fixture, or manual-review evidence.
- Fixture runner fails, review is blocked, or assistant check has `blockingStageIds`.
- The pack contains real saves, private exports, secrets, unauthorized assets, or runner artifacts.

When stopping, report: current stage, blocker, existing evidence, exact requested input, and the condition for resuming.
