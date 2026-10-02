---
name: bash-no-shell-grep-cat
description: "Use the grep/read/glob tools, not shell grep/rg/cat/head/tail"
condition: "\\b(grep|rg|cat|head|tail)\\b"
scope: "tool:bash"
---

Don't read or search files with shell `grep`/`rg`/`cat`/`head`/`tail`. Use `read` (supports `path:50-200`, `:raw`), `grep` for search, `glob` for discovery. Piped filter (`... | head/tail/grep`) on live output is fine. Reserve bash for fact pipelines (`wc`, `sort`, `diff`).