---
name: kvm-relay
description: Procedure for relaying a user prompt through the NanoKVM MCP to Antigravity (desktop app or agy CLI) on the remote Mac and reading the output back. Use for every user message in this project.
---

# KVM relay procedure

Target: macOS. Cmd = `meta: true`. Default target is the **agy CLI in Terminal** (cheapest to read). Use the Antigravity desktop app only when the user says "desktop" / "agy desktop" / `/agy-desktop`.

## Screenshots (the only return channel — keep them cheap)
- Default: `nanokvm_screenshot(max_width=1280, max_height=800, quality=50)`.
- Need to read small text: `max_width=1920, max_height=1200, quality=60`. Never go above.
- One screenshot per check. Do not screenshot after every keystroke.
- Mouse coordinates are in NANOKVM_SCREEN_WIDTH x HEIGHT space. If you used a downscaled screenshot, scale coordinates up by the same ratio. Prefer keyboard over mouse.

## Focusing an app (keyboard only)
1. `nanokvm_send_key(key="space", meta=true)` (Spotlight)
2. `nanokvm_send_text("Terminal")` or `"Antigravity"`, then `nanokvm_send_key("enter")`.
Only re-focus if the last screenshot shows the wrong app.

## CLI (agy in Terminal)
- If no agy session is running (shell prompt visible): `nanokvm_send_text("agy")`, `enter`, wait for its prompt.
- Send the user's prompt with `nanokvm_send_text(<prompt>)` (split into ≤1000-char chunks; for multi-line text use the agy multi-line convention shown on screen, else join lines with spaces), then `nanokvm_send_key("enter")`.
- Wait: take a screenshot; if agy is still working (spinner / "thinking" / streaming), screenshot again after a short pause. Back off — don't poll rapidly.
- When idle, transcribe only the **new** output since the prompt. If it scrolls past one screen, use `nanokvm_scroll(-5)` / `pageup` and stitch, reporting only once.
- If agy asks for approval/confirmation, show the user the exact question and options; send their answer as-is.

## Desktop (Antigravity app)
- Focus the agent chat input (click it once; remember its coordinates for the session).
- `nanokvm_send_text(<prompt>)`, `enter`, then wait/read as above, transcribing only the agent's reply.

## Special keys the user may ask for
`ctrl+c` → `send_key("c", ctrl=true)`, `esc` → `send_key("escape")`, `y`/`n` → `send_text`, `up` for history.
Stuck keyboard/mouse → `nanokvm_reset_hid()`. Black screen → `nanokvm_hdmi_status()` then tell the user.

## Reply format to the user
Plain transcription of target output in a code block, plus at most one line of status (e.g. "agy waiting for approval"). Nothing else.
