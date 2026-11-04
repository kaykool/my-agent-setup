# harness-sync

`harness-sync.py` refreshes model lists in the opencode, pi and pig configs from the Nutaraline and GutsAI gateways, upgrades the harness binaries, then updates their extensions. The module docstring covers usage and exit codes; `--help` lists the flags.

## Run it

- Preview with `python3 harness-sync.py --dry-run --diff`. A real run writes config files and may run `sudo pacman -S opencode`, so run it only when the user asks.
- The `harness-sync.py` command is a symlink at `~/.local/bin/harness-sync.py` that points into this folder; repoint it if the file moves.
- Targets come from the harness table in `catalog.md` (`--catalog`), not from this folder.
- To test a function, load the file with `importlib.util.spec_from_file_location`, add the module to `sys.modules`, then call `exec_module`. `@dataclass` fails without the `sys.modules` entry.

## Rules the code enforces

- **Online only:** GutsAI entries with `status != "ok"` are dropped. Nutaraline reports no status, so its list is taken as served.
- **Free default:** pi and pig `settings.json` get `defaultProvider` and `defaultModel` forced to a free model: the current one if free, else the first free one (Nutaraline before GutsAI). If no free model exists, the default is only repaired when it is gone.
  - Nutaraline is free when `pricing.free` is true or `pricing.discount_percent >= 100`.
  - GutsAI is free when `price_per_1m == 0` and `price_per_image` is absent.
- **Managed providers only:** `enabledModels` and defaults are pruned only for providers in the fetched catalog. Refs to built-in providers such as `anthropic` stay.
- opencode's default model is not managed.
- **Extensions:** `pi|pig update --extensions` and `opencode plugin update` run after the binary upgrades. pi and pig report changed packages by comparing `package.json` versions from `<harness> list` before and after; opencode only reports that the check ran.

## Gotchas

- Binary and extension updaters run through `run_interactive` and share the terminal, so sudo and update prompts work. Their output cannot be prefixed per line; pass `label=` so a `-> pi: extensions` line names the owner first. Keep them out of `step()`.
- npm audit warnings (for example "2 high severity vulnerabilities") in updater output come from npm, not from this script.
- Both gateways return 403 to the default `python-urllib` User-Agent, so `USER_AGENT` is set.
- The opencode JSONC file is reserialized with `json.dumps`, so its comments are lost on every change.
- `write_atomic` replaces the target file, so a symlinked config becomes a regular file.
- Backups go to `~/.local/state/harness-sync/backups/<UTC stamp>/`, named `<harness>-<file>`; the newest 10 are kept.
