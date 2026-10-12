---
name: kvm-relay
description: How to drive agy (Antigravity CLI) or the Antigravity desktop app on the remote Mac through the NanoKVM built-in MCP server (cube_* tools), using the kvm-agy helper to get clean, pageable output. Use whenever typing into, waiting on, or reading the remote Mac.
---

# Driving agy on the remote Mac through the NanoKVM

## Tools (NanoKVM firmware MCP; server prefix may vary, tool names don't)
| Tool | Args |
|---|---|
| `cube_type_text` | `text` ≤1000 chars (`\n` = Enter; non-US-ASCII chars are skipped), `delayMs` (default 30) |
| `cube_press_keys` | `keys`: one combo of KeyboardEvent.code names, e.g. `["MetaLeft","Space"]` |
| `cube_screenshot` | `quality` (device clamps 51–60) |
| `cube_move_mouse` / `cube_click_mouse` | `mode:"absolute"`, `x`,`y` as **0–1 fractions** of the screen |
| `cube_scroll_mouse` | `deltaY` (+ = down) |

Keys: `MetaLeft` (Cmd), `ControlLeft`, `AltLeft`, `ShiftLeft`, `Enter`, `Escape`, `Tab`, `Space`, `Backspace`, `ArrowUp`…, `KeyA`–`KeyZ`, `Digit0`–`Digit9`.

## The helper: `~/.agy-relay/kvm-agy`
Installed on the Mac (see `/kvm-setup`). Every screen it draws ends with a status line:
- `== KVM-AGY INPUT … ==`: waiting for prompt lines, ends at a line `::end`
- `== KVM-AGY WORKING 42s == <last progress line>`: agy is running
- `== KVM-AGY DONE exit=0 97s | page 3/3 ==`: finished; screen shows the **last page** of output

It runs `agy --dangerously-skip-permissions -p "<prompt>"` and adds `-c` automatically after the first run in a folder, so agy keeps the conversation going for that project.
If a screenshot shows `command not found` / `No such file` for the helper, stop and return `kvm-agy helper not installed; run /kvm-setup`.

## Procedure
0. **Focus Terminal** only if the last screenshot (or none yet this session) suggests it isn't in front: `["MetaLeft","Space"]`, type `Terminal`, `["Enter"]`. A black screen means the Mac is asleep: press `["ShiftLeft"]` and screenshot again.
1. **cwd** (if given and not "unchanged"): type `cd "<path>" && clear && pwd\n`. On `No such file or directory`, type `ls <projects root>\n`, screenshot, and return the folder list asking which one. Never guess.
2. **ask**: type `~/.agy-relay/kvm-agy ask\n`, then the text, then `\n::end\n`.
   - Text over 1000 chars: send it in ≤1000-char chunks, splitting at whitespace.
   - ASCII normalization only: curly quotes → straight, en/em dash → `-`, ellipsis → `...`, nbsp → space. If other non-ASCII characters are present, mention in the status line that they were dropped.
   - Don't screenshot while typing.
3. **Wait**: `sleep` 15, then `cube_screenshot`. While it shows `WORKING`, sleep with backoff (30, 60, 60, 120 … capped at 120s) and screenshot again. Report nothing in between.
4. **Read**: on `DONE`, transcribe the output text above the status line exactly (code stays code). Don't summarize. Return it plus the status line.
5. **page N**: type `~/.agy-relay/kvm-agy page N\n`, one screenshot, transcribe.
6. **new**: type `~/.agy-relay/kvm-agy new\n`. Return `new conversation on next ask`.
7. **cd** (switch only): after step 1, return `in <path>`.

If the screen shows anything unexpected (dialog, password prompt, macOS alert, agy login/auth request), don't click through it: return a one-line description of exactly what it says.

## Desktop app (only when asked)
Focus `Antigravity` via Spotlight. Screenshot once and find the agent chat input; click it (`cube_click_mouse` with fractions = pixel / image size). Type the text (use `["ShiftLeft","Enter"]` for line breaks; a bare `\n` sends early), then `["Enter"]`. Wait as in step 3 until the reply stops changing between two screenshots, then transcribe only the agent's reply. Scroll with `cube_scroll_mouse` if needed.

## Interactive agy TUI (fallback if the helper can't be used)
`agy --dangerously-skip-permissions` in Terminal. Newline in the input = `["ShiftLeft","Enter"]`, submit = `["Enter"]`, half-page scroll = `["ControlLeft","KeyU"]` / `["ControlLeft","KeyD"]`. It's done when the input prompt returns and nothing is streaming.
