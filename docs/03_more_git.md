Beyond the basics of tracking changes and collaborating, Git offers a range
of powerful features for managing complex projects. These features allow you to
streamline your workflow, experiment safely with new ideas, and maintain a
clean and organized project history. Exploring these more advanced aspects of
Git can significantly enhance your development process.

- `git reset --hard <sha>` throws away the working tree AND index — the commits
  are still in reflog and can be recovered for a while.
- `git reset --soft` keeps changes staged; `git reset --mixed` (default) keeps
  changes unstaged. Learn to pick the right one.
- `git revert <sha>` is the safe, shareable undo: it creates a new commit that
  reverses an old one, so already-pushed history stays intact.
- Emergency escape hatch: `git reflog` lists every position HEAD has held, and
  `git reset --hard <reflog-sha>` brings a lost commit back.
- `git restore --source <sha> -- <file>` pulls a single file back from an old
  commit.

- **Merge** — joins two histories with a merge commit. Preserves exactly what
  happened, nothing is rewritten. Default and safest for shared branches.
- **Rebase** (`git switch feature` then `git rebase main`) replays your commits
  on top of main under new SHAs, producing a linear history. Rebase _your own
  unpushed work_ onto an updated base often; never rebase shared/public
  branches.
- **Squash merge** — GitHub collapses the whole PR into a single commit on the
  target branch. Great for feature branches, loses individual commit
  granularity.
- Rule of thumb: squash your own feature branch PRs, merge shared release
  branches, rebase to stay current while you work.
- `git rebase --continue` / `--skip` / `--abort` handle conflicts mid-rebase.

- `git stash` shelves your uncommitted changes and gives you a clean tree —
  useful when you need to switch branches urgently or pull in someone else's
  work.
- `git stash pop` re-applies the stash and deletes it; `git stash apply`
  re-applies but keeps it; `git stash drop` discards.
- `git stash list` shows what you have; the stash itself is a commit reachable
  by `git stash show -p`.
- Stashes are local only — they are not pushed. Do not use stash as long-term
  storage; commit to a WIP branch instead.

- `git cherry-pick <sha>` applies the changes from one specific commit onto your
  current branch, creating a new commit with a new SHA.
- Use it to pull a single fix from another branch without merging or rebasing
  the whole thing — e.g. hotfixing a release branch.
- `git cherry-pick A..B` applies a range; `-x` records the original SHA in the
  commit message for traceability.
- `git cherry-pick --no-commit` applies changes without committing, so you can
  clean them up first.
- Abort a conflicted cherry-pick with `git cherry-pick --abort`.
- Cherry-picking duplicates history, so reach for rebase or merge first when a
  whole branch is the goal.
