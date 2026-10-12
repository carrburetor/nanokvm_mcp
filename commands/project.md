---
description: Switch the remote Mac to a project folder (agy keeps a conversation per folder), optionally sending a task
argument-hint: <project-name> [task...]
---
First word = project name; resolve it with CLAUDE.md (known projects, else `<projects root>/<name>`). Delegate to `kvm-worker`: cwd = that path; action `ask` with the rest as verbatim text, or action `cd` if nothing follows.

$ARGUMENTS
