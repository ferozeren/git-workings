# Large Repos

When a repo is big - lots of files, a long history, several subprojects -
the default commands start to crawl and the default layout stops fitting.

## Worktrees

A worktree is a second working directory for the same repository, so two
branches can be checked out at once without stashing.

```sh
git worktree add ../hotfix v2.4.0     # new branch from a tag
git worktree add -b feature ../feature
git worktree list
git worktree remove ../hotfix
```

- Useful for reviewing a PR locally while your main checkout stays on
  `main`.
- Only one worktree can have a branch checked out. `git worktree lock <path>`
  stops it being pruned.
- `git worktree prune` cleans up directories whose folder was deleted by hand.
- Each worktree has its own index and `HEAD`, but shares `.git` and the object
  store - no extra clone cost.
- Not for branches already checked out elsewhere; Git refuses rather than
  corrupting state.

## Submodules vs Subtree

- **Submodule** - a pointer to another repo's SHA, recorded in the tree. The
  other repo is a separate checkout at `<path>/.git`-like `.git` file.
  - `git submodule add <url> <path>`
  - `git submodule update --init --recursive` after a fresh clone
  - `git submodule update --remote` to move it forward
  - Set `git config --global submodule.recurse true` if you always want this.
- **Subtree** - copies the files into your history. No extra clone, no
  `.gitmodules`, but the history is mixed in.
  - `git subtree add --prefix vendor/lib <url> <branch>`
  - `git subtree pull --prefix vendor/lib <url> <branch>`
- Choose a submodule when the dependency has its own releases and you want a
  pinned SHA. Choose a subtree when you want zero ceremony.
- Submodules are widely disliked in CI and in onboarding. Consider a package
  manager instead if the dependency is a library.

## Reducing What You Clone

- Shallow: `git clone --depth 1 <url>`. Deepen later with
  `git fetch --deepen=50`.
- Single branch: `git clone --single-branch --branch <name> <url>`.
- Blobless partial clone stores no file contents locally and fetches them on
  checkout: `git clone --filter=blob:none <url>`. Combine with `--depth`.
- Sparse checkout keeps the whole history but only materialises some folders:

```sh
git sparse-checkout init --cone
git sparse-checkout set docs src
git sparse-checkout add scripts
```

- Partial clone plus sparse checkout is the usual answer to "this repo is too
  slow to open".
- Neither shrinks history permanently; to truly drop it, `git filter-repo` (see
  below) and force-push, which breaks every existing clone.

## Rewriting History at Scale

- `git filter-repo` (Python, not bundled) removes a file from all of history,
  splits a repo, or renames paths.
- `git filter-branch` is the older built-in and is deprecated. Do not start new
  work with it.
- Requires a force-push to `main`, so only do it on a repo nobody else has
  cloned. On a shared repo, prefer `git revert`.
- `git gc --aggressive --prune=now` reclaims space after big operations, but
  only after every reflog entry for the dropped objects has expired. Be
  patient.

## Large Files

- **Git LFS** stores binary content in a remote service and keeps a pointer in
  Git. Without it, binaries bloat every clone permanently.
  - `git lfs install`, then `git lfs track "*.psd"`, then `git add .gitattributes`.
  - Commit the `.gitattributes` file; everyone else gets the rule automatically.
  - `git lfs ls-files`, `git lfs pull` after a fresh clone.
- LFS needs a quota plan on GitHub; free accounts have 1 GB storage / 1 GB
  bandwidth, and billing counts against your account.
- Add a `.gitignore` rule before anything is committed. Once tracked, use
  `git rm --cached <file>` and add `git filter-repo --path <file>
--invert-paths`.
- Keep build output out: `dist/`, `node_modules/`, `target/`, `*.class`.
  A stray `node_modules` in history is permanent.

## Monorepos

- One repo with many packages. Simplifies cross-package changes and CI
  sharing; complicates permissions, CI times and everyone else's clone.
- Keep CI selective with path filters (`on: push: paths:` in
  `.github/workflows/`) so unrelated packages do not trigger every pipeline.
- Larger histories call for the partial clone and sparse checkout above.
- Splitting a repo later: `git filter-repo --sparse --path src/api/` per
  subdirectory.
- Alternative: separate repos plus a package registry. Simpler permissions,
  versioning per package, more dependency bookkeeping.

## When It Is Still Too Slow

- `git gc` periodically reclaims space; add it to a scheduled job if builds
  clone many repos.
- Prefer `git status -sb` and `git diff --stat` over full diffs on big changes.
- Set `core.fsmonitor` and `core.untrackedCache` on large working trees.
- Deep history plus many branches: clone shallow for day-to-day work and fetch
  specific SHAs only when needed.
