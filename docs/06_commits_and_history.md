# Commits and History

History quality is a product decision. A log that reads like a changelog means
`git bisect`, `git blame` and code review all work for you. A log of
"fixes" and "wip" does not.

## Writing Commits

- Summary line in imperative mood, ≤50 characters: `Add retry to fetch`,
  not `Added retry` or `adds retry`.
- Blank line, then a body that answers **why**. The diff already says what
  changed; repeating it is noise.
- Reference issues in the body or trailer: `Closes #123`, `Refs #45`.
  GitHub closes the issue on merge when the keyword is there.
- `git commit -m "summary" -m "body"` avoids opening an editor.
- `git commit -a` stages tracked modifications, never new files - a common
  surprise. Use `git add -A` if you want everything.
- Empty commits are fine as markers: `git commit --allow-empty -m "Start work
on X"`.
- `git commit -S` signs the commit.

## Conventional Commits

A convention makes history machine-readable, which unlocks changelogs and
automated version bumps.

```
<type>(<scope>): <subject>

type: feat | fix | docs | style | refactor | perf | test | build | ci | chore
```

- `feat(auth): add SSO login` → minor version bump.
- `fix(api): null-check user id` → patch bump.
- A `BREAKING CHANGE:` footer, or `feat!:` in the subject, triggers a major
  bump.
- Works with `standard-version`, `semantic-release`, or commitizen for local
  enforcement.

## Fixing What You Already Committed

```sh
git add <missing-file>
git commit --amend --no-edit      # fold into the last commit

git commit --fixup <sha>          # stage a fix for that commit
git rebase -i --autosquash         # squash it in automatically
```

- `--autosquash` reorders `--fixup` and `--squash` commits under their targets,
  so an amend-like fixup after several commits still lands in the right place.
- `git rebase -i HEAD~5` opens an editor listing the last five commits. Actions
  are `pick`, `reword`, `edit`, `squash`, `fixup`, `drop`, `exec`.
- Autosquash usually kicks in by default on `git commit --fixup` in recent
  Git; `--no-autosquash` turns it off.
- Only rewrite commits you have not pushed. `edit` lets you amend mid-rebase and
  `git rebase --continue`.

## Tags and Releases

- `git tag v1.2.0` - lightweight, a bare pointer. Fine for local use.
- `git tag -a v1.2.0 -m "Release 1.2.0"` - annotated, with author, date and
  message. Use this one for anything you push.
- Push a tag explicitly: `git push origin v1.2.0` (or `--tags` for all).
- Deleting a pushed tag is a rebase-in-disguise: `git push origin :v1.2.0`.
- Create a GitHub release from an existing tag with `gh release create v1.2.0`,
  which can auto-generate notes with `--generate-notes`.
- `git tag --sort=-creatordate` lists tags newest first;
  `git describe --tags` names the current commit relative to the nearest tag.
- Signed tags (`git tag -s -a`) are the standard for verifiable releases.

## Working With History Without Rewriting It

- `git rebase --onto <newbase> <oldbase> <branch>` moves a branch onto a new
  base while keeping its commits - the surgical form of rebase.
- `git rebase --keep-base` rebases onto `main` and replays only your own
  commits on top of the upstream changes.
- `git replace` and grafts let you view history as something else locally,
  without changing the repo. Rare, and not pushed.
- `git log --follow <file>` traces a file's history across renames.
- `git blame -w` ignores whitespace-only changes, so the real author shows up.
- `git log -S"<string>"` finds the commits that added or removed an exact
  string - the fastest way to answer "when did this change".

## Notes and Extra State

- `git notes add <sha>` attaches a note to a commit; `git log --notes` shows
  them.
- Useful for review comments or TODO links that should not live in the message.
- Notes have their own `refs/notes/commits` ref and must be pushed separately:
  `git push origin refs/notes/commits`.
