# nanokvm_mcp — Claude as a KVM relay to Antigravity

Claude Code types your prompts into **Antigravity / `agy`** on a dedicated MacBook through a **NanoKVM**, then reads the screen back to you. All coding runs on the Mac; Claude here only relays.

## Contents
| Path | What it is |
|---|---|
| `.mcp.json` | NanoKVM firmware's built-in MCP server ([sipeed/NanoKVM](https://github.com/sipeed/NanoKVM), `/api/mcp`, tools `cube_*`) |
| `CLAUDE.md` | Relay-only rules (verbatim forwarding, no coding, token-frugal) |
| `skills/kvm-relay/` | Step-by-step procedure: focus app, type, submit, wait, transcribe |
| `agents/kvm-relay.md` | Haiku subagent that does the screenshot loop out of the main context |
| `commands/` | `/agy`, `/agy-desktop`, `/agy-bg`, `/screen`, `/key` |
| `.claude/settings.json` | Auto-allows the NanoKVM `cube_*` tools |
| `.claude-plugin/` | Same thing packaged as an installable plugin |

## Setup
1. NanoKVM web UI → **Settings > MCP**: enable it and copy the endpoint and API key.
2. Export them for Claude Code:
   ```sh
   export NANOKVM_URL=http://192.168.1.50      # no trailing slash; /api/mcp is appended
   export NANOKVM_MCP_KEY=<api key>
   ```
   Already added it in Claude Desktop? Name that server **`nanokvm`** so tool names match `mcp__nanokvm__cube_*` used by the agent/skill.
3. Run `claude` in this repo. (Or elsewhere: `/plugin marketplace add carrburetor/nanokvm_mcp` then `/plugin install nanokvm-relay@nanokvm-relay`.)
4. On the Mac: open Terminal (and/or Antigravity) and log in. Disable sleep/screen lock.

**Network:** Claude Code must be able to reach the NanoKVM's IP. A claude.ai cloud session can't see your LAN — run Claude Code on a machine on the same network, or expose the NanoKVM via Tailscale/VPN.

## Use
- Just type a message → forwarded verbatim to `agy`, output returned.
- `/agy-desktop <text>` → Antigravity desktop agent.
- `/agy-bg <text>` → same as `/agy` but in the cheap Haiku subagent.
- `/screen` → read current screen. `/key ctrl+c` → send a key.
