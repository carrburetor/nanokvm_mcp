# nanokvm_mcp — Claude as a KVM relay to Antigravity

Claude Code types your prompts into **Antigravity / `agy`** on a dedicated MacBook through a **NanoKVM**, then reads the screen back to you. All coding runs on the Mac; Claude here only relays.

## Contents
| Path | What it is |
|---|---|
| `.mcp.json` | NanoKVM MCP server ([scgreenhalgh/nanokvm-mcp](https://github.com/scgreenhalgh/nanokvm-mcp)) via `uvx` |
| `CLAUDE.md` | Relay-only rules (verbatim forwarding, no coding, token-frugal) |
| `skills/kvm-relay/` | Step-by-step procedure: focus app, type, submit, wait, transcribe |
| `agents/kvm-relay.md` | Haiku subagent that does the screenshot loop out of the main context |
| `commands/` | `/agy`, `/agy-desktop`, `/agy-bg`, `/screen`, `/key` |
| `.claude/settings.json` | Auto-allows NanoKVM tools; blocks power/ISO tools |
| `.claude-plugin/` | Same thing packaged as an installable plugin |

## Setup
1. Install [uv](https://docs.astral.sh/uv/) (provides `uvx`).
2. Export the NanoKVM connection info, e.g.:
   ```sh
   export NANOKVM_HOST=192.168.1.50
   export NANOKVM_USER=admin NANOKVM_PASS=yourpass
   export NANOKVM_SCREEN_WIDTH=1920 NANOKVM_SCREEN_HEIGHT=1080   # Mac's HDMI output resolution
   ```
3. Run `claude` in this repo. (Or elsewhere: `/plugin marketplace add carrburetor/nanokvm_mcp` then `/plugin install nanokvm-relay@nanokvm-relay`.)
4. On the Mac: open Terminal (and/or Antigravity) and log in. Disable sleep/screen lock.

**Network:** Claude Code must be able to reach the NanoKVM's IP. A claude.ai cloud session can't see your LAN — run Claude Code on a machine on the same network, or expose the NanoKVM via Tailscale/VPN.

## Use
- Just type a message → forwarded verbatim to `agy`, output returned.
- `/agy-desktop <text>` → Antigravity desktop agent.
- `/agy-bg <text>` → same as `/agy` but in the cheap Haiku subagent.
- `/screen` → read current screen. `/key ctrl+c` → send a key.
