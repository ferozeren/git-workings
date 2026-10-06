# Undo and Recovery

Git almost never destroys work. It moves pointers, and the pointers point at
objects that stay around for weeks. Learning to move them deliberately is the
difference between an hour of panic and a five-second fix.

## Which Undo Do I Want?

| Situation                               | Command                                    | History rewritten? |
| --------------------------------------- | ------------------------------------------ | ------------------ |
| Unstage a file                          | `git restore --staged <file>`              | No                 |
| Discard unstaged edits                  | `git restore <file>`                       | No                 |
| Fix the last commit message             | `git commit --amend`                       | Last commit only   |
| Add a forgotten file to the last commit | `git add <file>` then `git commit --amend` | Last commit only   |
| Undo a commit that nobody has pulled    | `git reset --soft HEAD~1`                  | Yes, local only    |
| Undo a commit that is already pushed    | `git revert <sha>`                         | No                 |
| Undo a merge commit                     | `git revert -m 1 <sha>`                    | No                 |
| Bring back a deleted branch             | `git branch <name> <sha>`                  | No                 |

The split is `reset` versus `revert`. `reset` moves HEAD, so the old commits
drop off the current branch. `revert` adds a new commit that undoes an old one,
so history stays intact for everyone.

## Reflog: The Undo Button Git Does Not Advertise

- `git reflog` lists every position `HEAD` has held, newest first, with the
  commit each one pointed at.
- `HEAD@{1}` means "where HEAD was one step ago". `HEAD@{5}` is five steps ago.
- Found it? `git reset --hard <sha>` puts the commit back on your branch.
- Also worth checking: `git reflog show <branch-name>` for a specific branch.
- Reflog is local only, so it is your safety net, not your collaborator's.
- Reflog entries expire after ~90 days by default (`gc.reflogExpire`). Recover
  sooner if you care.

## Recovering Other Kinds of Loss

- Deleted branch: `git branch <name> <sha-from-reflog>`. There is no
  `git branch --restore`; the reflog is the source of truth.
- Detached HEAD with uncommitted work: `git switch -` goes back to the previous
  branch and carries the changes along.
- One file from history: `git restore --source <sha> -- <path>`.
- A whole path from history: `git checkout <sha> -- <path>` writes it into the
  index and working tree.
- Truly dangling objects: `git fsck --lost-found` lists them, then
  `git show <sha>` to see if it is worth keeping.
- `git commit` with nothing staged? `git commit --amend` still works and now
  includes whatever you just staged.

## Bisecting a Bug

`git bisect` automates the "test every commit until you find the one that broke
it" loop. It works on any test that exits non-zero.

```sh
git bisect start
git bisect bad                 # current commit is broken
git bisect good v2.4.0         # last known-good tag
# Git checks out a midpoint commit; run your test, then:
git bisect bad                 # or: git bisect good
```

- `git bisect run <script>` automates the loop: give it a test command or a
  script and it classifies every commit for you.
- `git bisect good` / `bad` accept a commit directly:
  `git bisect good 1234abc`.
- `git bisect reset` returns you to the branch you started on. Always finish
  with it, or you stay detached.
- Catches up to `log2(n)` steps — ~10 steps for a thousand commits.
- If the bug spans multiple commits, bisect is not applicable; narrow it by hand
  with `git log -S` on the suspect string.

## Merges and Conflicts

- `git merge --abort` cancels a merge; `git rebase --abort` cancels a rebase;
  `git cherry-pick --abort` cancels a cherry-pick. There is always an out.
- `git revert -m 1 <merge-sha>` undoes a merge by keeping the first parent's
  history. The `-m` flag says which parent you kept — getting it wrong undoes
  the wrong side.
- `git diff --check` flags whitespace-only conflicts markers after you have
  hand-resolved a mess.

## Building Good Commits to Make Undo Easy

- One logical change per commit. `git add -p` lets you stage hunk by hunk.
- Write the message for the person doing `git blame` in two years — often
  yourself.
- A bad commit is easy to undo when the diff is small and the message is
  honest. Large mixed commits are the ones people cannot unpick.
