# Install (for agents)

You are installing `system-chewy`: a macOS terminal dashboard (CPU, memory, memory by process, storage, disk by project, network, Claude sessions, Claude plan usage) that keeps the Mac awake while it runs. Do these steps in order. Ask the user before changing anything in `~/.claude/`.

## 1. Check prerequisites

- macOS (network rates use `nettop`; `caffeinate` is macOS-only).
- `cargo` on PATH. If missing, ask the user before installing Rust via https://rustup.rs.
- `python3` on PATH (Claude sessions panel and statusline use it).

## 2. Point the project panel at the user's code

`src/main.rs` has `const PROJECT_ROOT: &str = "Documents/Lhava";` (relative to `$HOME`). Ask the user where they keep their code and set it to that path (e.g. `"code"` for `~/code`). If they don't care, leave it; the panel just stays empty.

## 3. Build and install under the `system-chewy` name

From the repo root:

```sh
cargo install --path .
ln -sf "$HOME/.cargo/bin/monitor" "$HOME/.cargo/bin/system-chewy"
```

The crate builds a binary called `monitor`; the symlink gives it the `system-chewy` name. Confirm with `command -v system-chewy`. If `~/.cargo/bin` isn't on PATH, tell the user to add it.

## 4. Feed the CLAUDE usage panel (optional)

The panel reads `~/.claude/statusline/usage.txt`, which `statusline/statusline.py` writes each time Claude Code refreshes its status line. Usage numbers only exist on Claude Pro/Max plans.

1. Read `~/.claude/settings.json`. If it already has a `statusLine`, **stop and ask** the user whether to replace it; don't clobber theirs.
2. Otherwise:
   ```sh
   mkdir -p ~/.claude/statusline
   cp statusline/statusline.py ~/.claude/statusline/statusline.py
   chmod +x ~/.claude/statusline/statusline.py
   ```
   and merge into `~/.claude/settings.json` (keep every existing key):
   ```json
   "statusLine": {
     "type": "command",
     "command": "<absolute path to ~/.claude/statusline/statusline.py>",
     "refreshInterval": 10
   }
   ```

Without this step everything else works; the CLAUDE panel shows "no usage cached".

## 5. Done

Tell the user: run `system-chewy`, `q`/`esc` quits. The panel fills once a Claude Code session is open.
