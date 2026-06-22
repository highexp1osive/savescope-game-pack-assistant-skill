# SaveScope Game Pack CLI Output

All Game Pack CLI commands should produce JSON.

## Exit Codes

| Code | Meaning |
| --- | --- |
| `0` | Success |
| `1` | Command/tool failure or invalid tool output |
| `2` | Validation, fixture, review, or assistant check blocked |
| `64` | Invalid arguments or usage |

Some wrappers display all non-zero exits as `1`; prefer the JSON `summary.exitCode`.

## Failure Envelope

Failures use:

```json
{
  "schemaVersion": 1,
  "tool": "validateGamePack",
  "status": "error",
  "error": {
    "code": "invalid-arguments",
    "message": "Missing Game Pack directory.",
    "details": []
  },
  "summary": {
    "exitCode": 64,
    "retryable": false
  }
}
```

Use `error.code` to decide the response:

| Code | Response |
| --- | --- |
| `invalid-arguments` | Fix command, path, or flags |
| `command-failed` | Inspect JSON/files/runtime error |
| `invalid-tool-output` | Treat as toolchain bug or schema drift |
| `validation-failed` | Fix Game Pack structure/content |
| `update-expected-blocked` | Do not force expected updates |

## Success Shapes

Wrapper commands with `status: "ok"`:

- `create:game-pack`
- `validate:game-pack`

Tool-schema commands without `status`:

- `test:game-pack`
- `review:game-pack`
- `inspect:save`
- `diff:saves`
- `assistant:game-pack check`

## Blocked But Structured

Some commands may emit complete JSON and still exit `2`:

- `test:game-pack` when fixture runner failed.
- `review:game-pack` when submission gate is blocked.
- `assistant:game-pack check` when summary status is blocked.

In these cases, use the JSON diagnostics and contributor tasks instead of rerunning blindly.
