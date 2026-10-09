# Hooks and Automation

Hooks are scripts Git runs at specific points. They are the cheapest way to
stop a mistake before it lands in history - and the easiest way to make Git
slow if you overdo it.

## Where Hooks Live

- Per-clone hooks: `.git/hooks/` - **not tracked by Git**, so they do not
  travel to other people.
- Shared hooks: a directory in the repo (commonly `.githooks/`) plus
  `core.hooksPath`, so hooks are committed and everyone gets them.
- System-wide: `git config --global core.hooksPath ~/.githooks`.
- `core.hooksPath` replaces the whole `.git/hooks` lookup, it does not merge
  with it. Put everything you need in one directory.
- Hooks are plain executables. On Windows you need a shebang handled by Git for
  Windows (Git Bash is present) - `#!/usr/bin/env bash` works, or use a `.ps1`
  with `#!/usr/bin/env pwsh`.
- Skip a hook once with `git commit --no-verify` (or `--no-verify` on `push`).
  Use it rarely and fix the underlying problem instead.

## Common Hooks

```sh
#!/usr/bin/env bash
# .githooks/pre-commit
set -euo pipefail
npx lint-staged          # or: npm test, ruff check, go vet
```

- `pre-commit` - before a commit is created. The right place for formatters,
  linters on staged files, and secret scanners.
- `commit-msg` - after the message is written, before it is accepted. Enforce
  commit format here.
- `prepare-commit-msg` - can rewrite the message file, used by templates.
- `pre-push` - before pushing. Run the full test suite; this is the last local
  gate.
- `post-commit` - after a successful commit; rarely needed.
- `post-merge` - after a merge; often used to reinstall dependencies after
  pulling a lockfile change.
- `post-checkout` - fires on every branch switch. Keep it fast or it will be
  felt on every `git status` that triggers a refresh.
- A hook that exits non-zero stops the operation. Exit 0 lets it pass.
- Hooks run from the repo root, but do not assume your shell is interactive;
  anything that prompts will hang CI.

## Installing the Shared Directory

```sh
git config core.hooksPath .githooks
```

- Do this once after cloning; add the same line to your setup notes or a
  bootstrap script.
- Document it in the README so contributors do not silently skip the hooks.
- CI should run the same checks independently - hooks are bypassable and
  `--no-verify` exists.

## Managed Hook Frameworks

- **pre-commit** (Python) - manages dozens of hooks from `.pre-commit-config.yaml`
  (`reorder-python-imports`, `check-yaml`, `detect-secrets`, ...). Caches
  environments and is fast.
- **husky** (Node) - the standard in JS repos; wires
  `lint-staged` so only staged files are linted.
- **lefthook** (Go) - fast, single binary, supports parallel jobs and glob
  filters.
- **simple-git-hooks** - no config file, just command strings.
- Keep it minimal: format, lint staged files, and one real test. Every extra
  hook costs everyone who clones.

## GitHub Actions as the Enforcement Point

Hooks are local and untrusted; CI is the authority.

```yaml
name: ci
on:
  pull_request:
  push:
    branches: [main]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm test
```

- `npm ci` not `npm install` in CI - it installs exactly the lockfile.
- Protect `main` and require the status check to pass, so the workflow is
  actually enforced.
- Add a `.github/workflows` job that runs `pip install pre-commit` then
  `pre-commit run --all-files` to get the same checks CI and locally.
- Keep secrets in repository secrets, never in the YAML. `pull_request_target`
  runs with write access - do not check out untrusted PR code under it.

## Other Automation Worth Knowing

- `git bisect run <script>` uses any script as a test oracle - the cleanest way
  to find the commit that broke something.
- `git rerere <cmd>` records how you resolved conflicts and replays them, which
  pays off badly when a long-running rebase keeps hitting the same conflict.
  `git rerere status` shows what is being remembered.
- `git maintenance start` (Git 2.30+) schedules `gc`, commit-graph and
  pack-refs pruning automatically.
- Release automation: conventional commits plus `semantic-release` turns merge
  to `main` into a version bump and a tag.
