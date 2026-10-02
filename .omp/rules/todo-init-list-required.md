---
name: todo-init-list-required
description: "Todo init must include list field"
condition: "Bare `init` without `list`"
scope: "text"
---

Todo `init` replaces the whole list and requires `list: [{phase, items: [...]}]`. Never call `init` without `list` — it is rejected with `Missing list`. If phases are unknown, use one phase: `list: [{phase: Tasks, items: [...]}]`.