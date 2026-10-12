# nanokvm_mcp: Claude as a KVM relay to Antigravity

Claude Code types your prompts into **agy** (Antigravity CLI) on a dedicated MacBook through a **NanoKVM**, then reads the result off the screen. All coding runs on the Mac; Claude only relays.

```
you ─▶ kvm-relay (main agent, Haiku: routes, never codes)
          └─▶ kvm-worker (subagent, Sonnet: types, waits, reads screenshots)
                 └─▶ NanoKVM built-in MCP (cube_* tools) ─HDMI/USB─▶ Mac Terminal
                        └─▶ ~/.agy-relay/kvm-agy ─▶ agy --dangerously-skip-permissions -p … [-c]
```

## Why it's built this way
- **Main session is a thin router.** `.claude/settings.json` sets `"agent": "kvm-relay"`, so the main thread uses a short system prompt, has no Bash/Edit/Write, and can't drift into coding.
- **Screenshots stay out of the main context.** Every screenshot-heavy step runs in the `kvm-worker` subagent, which returns only transcribed text, so a long session doesn't keep resending images.
- **Headless agy behind a helper.** `remote/kvm-agy` on the Mac takes the prompt from stdin, so quotes, `$` and backticks are never parsed by the shell and there's no 1024-character line limit. It runs agy headless with `-c` for per-folder conversations, shows `== KVM-AGY WORKING/DONE ==` so one screenshot tells whether it's finished, and pages long output.

## Contents
| Path | What it is |
|---|---|
| `agents/kvm-relay.md` | Main-session router (Haiku) |
| `agents/kvm-worker.md` | Subagent that drives the KVM (Sonnet; preloads the skill) |
| `skills/kvm-relay/` | Exact procedure: tools, keys, helper protocol, waiting/backoff, desktop fallback |
| `commands/` | `/project`, `/agy`, `/more`, `/agy-new`, `/agy-desktop`, `/screen`, `/key`, `/kvm-setup` |
| `remote/kvm-agy` | Helper script installed on the Mac |
| `CLAUDE.md` | Your settings: projects root, project aliases |
| `.claude/settings.json` | Main agent + auto-allow NanoKVM tools and `sleep` |
| `.claude-plugin/` | Same thing as an installable plugin (prompts for NanoKVM URL and key) |

## Setup
1. **NanoKVM**: web UI → **Settings > MCP**, enable it, copy the endpoint and API key.
2. **Claude Code MCP**: add the server, named `nanokvm`:
   ```sh
   claude mcp add --transport http --scope user nanokvm http://<nanokvm-ip>/api/mcp \
     --header "Authorization: Bearer <api key>"
   ```
   If you already added it under a different name, it still works, but add `mcp__<yourname>__*` to `permissions.allow` in `.claude/settings.json` or you'll get permission prompts.
3. **Mac**: log in, disable sleep and screen lock, Terminal full-screen with a large (16–18pt) high-contrast font. Make sure `agy` runs and is signed in.
4. **Helper**: run `claude` in this repo and type `/kvm-setup`. It types `remote/kvm-agy` into `~/.agy-relay/kvm-agy` on the Mac. You can also copy it over yourself.
5. Set your projects root in `CLAUDE.md`.

Claude Code must run on a machine that can reach the NanoKVM (same LAN, or Tailscale/VPN). A claude.ai cloud session can't.

As a plugin elsewhere: `/plugin marketplace add carrburetor/nanokvm_mcp`, `/plugin install nanokvm-relay@nanokvm-relay`, then `claude --agent nanokvm-relay:kvm-relay`.

## Use
- "Let's go to project foo and implement X" → `cd ~/Projects/foo`, agy runs "implement X", last page of output comes back.
- Any other message → sent to agy in the current project, continuing that conversation.
- `/more` (or "show more") → previous page. `/agy-new` → fresh conversation.
- `/agy-desktop <text>` → Antigravity desktop app instead of the CLI.
- `/screen` → read the screen. `/key ctrl+c` → send a key (also stops a running agy).
