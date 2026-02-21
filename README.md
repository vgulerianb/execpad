# runmix

Run **bash**, **python**, **javascript** (Node), and **sql** (sqlite3 CLI) against a **real directory** — for AI agents and tooling.

```bash
npm install runmix
```

```ts
import { Runtime } from "runmix";

const rt = new Runtime("./my-project");
const r = await rt.run("python", `print(open("README.md").read()[:80])`);
console.log(r.stdout);
rt.close();
```

- **Read-only** workspace: `new Runtime("./repo", { readonly: true })`
- **Overlay** (copy workspace to temp, `apply()` to write back): `{ overlay: true }`
- **Persistence**: `serialize()` / `Runtime.deserialize()` for session restore
- **Globs**: `includeGlobs` / `excludeGlobs` for file-change tracking scope
- **OpenAI**: `asOpenAITool()` + `executeToolCall(args)`
- **Session log**: each `run()` is recorded (stdout/stderr, code, cwd, exit, file changes). Use `getRunLog()`, `clearRunLog()`, optional `onRun` callback, `exportRunLogJSON` / `exportRunLogMarkdown`. Disable with `{ runLog: false }` or cap with `runLogMaxEntries`.

### Session log (TypeScript)

```ts
import { Runtime, exportRunLogJSON } from "runmix";

const rt = new Runtime("./repo", {
  onRun: (e) => console.error(e.stderr), // stream errors to your logger
});
await rt.run("bash", "echo hi");
console.log(exportRunLogJSON(rt.getRunLog()));
```

Python: `get_run_log()`, `clear_run_log()`, `export_run_log_json`, `export_run_log_markdown`; constructor args `run_log=False`, `run_log_max_entries`, `on_run`.

### Possible next features

- **Streaming stdout/stderr** (line chunks via callback while the process runs).
- **Structured exit reasons** (timeout vs kill vs spawn error) and optional **core dump** metadata.
- **Allowlist / denylist** for binaries and `env` key prefixes.
- **Resource metrics** (peak RSS via `ps` or `resource` on Unix).
- **First-class `stdin`** and **PTY** mode for TUI agents.
- **WebSocket or HTTP bridge** so remote agents attach to the same `Runtime`.
- **Plugin engines** (register a custom `Language` handler).

Python: `pip install ./python` from `runmix/python` (see `python/README.md`).

SQL on Node requires `sqlite3` on your PATH. Use the Python package for stdlib `sqlite3` only.


License: Apache-2.0
