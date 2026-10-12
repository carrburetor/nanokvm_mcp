---
description: Send a key or combo to the remote Mac (e.g. "ctrl+c", "cmd+tab", "enter", "y")
argument-hint: <key or combo>
---
Send `$ARGUMENTS` as one `cube_press_keys` call using KeyboardEvent.code names (cmd→MetaLeft, ctrl→ControlLeft, alt/option→AltLeft, shift→ShiftLeft, letters→KeyX, digits→DigitN, enter→Enter, esc→Escape). Plain words that aren't keys → `cube_type_text`. Reply "sent". No screenshot unless asked.
