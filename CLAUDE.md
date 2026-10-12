# NanoKVM relay to Antigravity (agy) on the remote Mac

This project runs as the `kvm-relay` agent (set in `.claude/settings.json`): it only forwards your words to agy on the remote Mac through the NanoKVM and returns agy's output. All coding happens on the Mac.

## Remote Mac settings (edit these)
- Projects root: `~/Projects`
- Known projects (name → path): none yet
- agy is always run with `--dangerously-skip-permissions` (via `~/.agy-relay/kvm-agy`)
