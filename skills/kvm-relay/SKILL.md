---
name: kvm-relay
description: Procedure for relaying a user prompt through the NanoKVM built-in MCP server (cube_* tools) to Antigravity (desktop app or agy CLI) on the remote Mac and reading the output back. Use for every user message in this project.
---

# KVM relay procedure

Target: macOS via the NanoKVM firmware's built-in MCP server (`mcp__nanokvm__cube_*`).
Default target is the **agy CLI in Terminal** (cheapest to read). Use the Antigravity desktop app only when the user says "desktop" / `/agy-desktop`.

## Tools (built-in NanoKVM MCP)
| Tool | Args |
|---|---|
| `cube_type_text` | `text` (≤1000 chars), `delayMs` (0–1000, default 30) |
| `cube_press_keys` | `keys`: one shortcut as KeyboardEvent.code names, e.g. `["MetaLeft","Space"]`; `holdMs` |
| `cube_screenshot` | `quality` (device clamps to 51–60), `timeoutMs` |
| `cube_move_mouse` | `mode:"absolute"`, `x`,`y` **normalized 0–1**; or `mode:"relative"`, `deltaX`/`deltaY` |
| `cube_click_mouse` | `button`, `mode:"absolute"`, `x`,`y` (0–1); or `mode:"relative"` with no coords |
| `cube_scroll_mouse` | `deltaY` (positive = down) |

Key names: `MetaLeft` (Cmd), `ControlLeft`, `AltLeft` (Option), `ShiftLeft`, `Enter`, `Escape`, `Tab`, `Space`, `Backspace`, `ArrowUp/Down/Left/Right`, `PageUp/PageDown`, `KeyA`–`KeyZ`, `Digit0`–`Digit9`, `F1`–`F12`.
Examples: Cmd+Space `["MetaLeft","Space"]`, Ctrl+C `["ControlLeft","KeyC"]`, Cmd+Tab `["MetaLeft","Tab"]`.

## Screenshots (the only return channel — keep them rare)
- `cube_screenshot(quality=51)`. One per check; never after every keystroke.
- Mouse coords are fractions of the screen: x = px / image_width, y = px / image_height. Prefer keyboard over mouse.

## Focusing an app (keyboard only)
`cube_press_keys(["MetaLeft","Space"])` → `cube_type_text("Terminal")` (or `"Antigravity"`) → `cube_press_keys(["Enter"])`.
Only re-focus if the last screenshot shows the wrong app.

## Switch project (CLI)
1. Focus Terminal. If an agy session is running, exit it first (`/exit` + Enter, or Ctrl+C twice) so you're at a shell prompt.
2. Resolve the path: a "Known projects" entry from CLAUDE.md, else `<projects root>/<name>`.
3. Type `cd "<path>" && clear && pwd`, Enter. Screenshot once.
   - If it says "No such file or directory": type `ls <projects root>`, Enter, screenshot, show the user the list and ask which one. Don't guess.
4. Type `agy`, Enter; wait for its prompt (one screenshot).
5. If the user gave a task with the switch, send it now as below.
Reply to the user with one line: `In <path>, agy ready.` plus agy's output if a task was sent.

## Switch project (desktop)
Focus Antigravity, Cmd+O (`["MetaLeft","KeyO"]`), Cmd+Shift+G (`["MetaLeft","ShiftLeft","KeyG"]`), type the path, Enter, Enter. Screenshot once to confirm, then send any task in the agent chat.

## CLI (agy in Terminal)
- If a shell prompt is showing (no agy session): type `agy`, `Enter`, wait for its prompt.
- Send the user's text with `cube_type_text` (split into ≤1000-char chunks, `delayMs: 10` for long text). Join multi-line text with spaces unless agy shows a multi-line convention. Then `cube_press_keys(["Enter"])`.
- Wait, then screenshot. If agy is still working (spinner / thinking / streaming), wait longer and screenshot again. Back off; don't poll rapidly.
- When idle, transcribe only the **new** output since the prompt. If it ran off-screen, `cube_scroll_mouse(deltaY=-5)` and stitch; report once.
- If agy asks for approval/confirmation, show the user the exact question and options; send their answer as-is.

## Desktop (Antigravity app)
- Click the agent chat input once (remember its normalized coords for the session), type, `Enter`, then wait/read as above, transcribing only the agent's reply.

## Special keys the user may ask for
ctrl+c → `["ControlLeft","KeyC"]`, esc → `["Escape"]`, y/n → `cube_type_text`, history → `["ArrowUp"]`.
Tool errors about control mode / lease: the NanoKVM MCP is disabled or another controller (web UI, PicoClaw) holds input — tell the user.

## Reply format to the user
Plain transcription of target output in a code block, plus at most one status line (e.g. "agy waiting for approval"). Nothing else.
