---
description: One-time install of the kvm-agy helper on the remote Mac by typing it through the KVM
---
Read the helper script: `remote/kvm-agy` in this project (or `${CLAUDE_PLUGIN_ROOT}/remote/kvm-agy` when installed as a plugin). Then, using `cube_*` tools directly:
1. Focus Terminal (Cmd+Space, "Terminal", Enter).
2. Type `set +H 2>/dev/null; setopt nobanghist 2>/dev/null; mkdir -p ~/.agy-relay && cat > ~/.agy-relay/kvm-agy <<'KVM_EOF'\n`
3. Type the script contents exactly, in ≤1000-char chunks split at line ends, `delayMs: 10`.
4. Type `KVM_EOF\nchmod +x ~/.agy-relay/kvm-agy && shasum ~/.agy-relay/kvm-agy && ~/.agy-relay/kvm-agy status\n`
5. One screenshot. Reply with the shasum shown and whether it printed `== KVM-AGY`. Compare against the local file's sha1 (the user can check with `shasum remote/kvm-agy`).
