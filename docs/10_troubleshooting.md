# Troubleshooting

Symptom first, then command. Most Git problems come down to one of three
causes: history diverged, the index and working tree disagree, or Git refuses
because it would lose work.

## "Your branch is behind/ahead of origin"

- Behind: your clone is missing remote commits. `git fetch origin` then
  `git rebase origin/main` (or `git merge origin/main`).
- Diverged: both sides moved. Rebase your unpushed work,
  `git rebase origin/main`, or merge if the remote commits are shared.
- You do not actually want the remote changes: `git reset --hard origin/main`
  matches your branch to it and throws away your commits. Only if you know they
  are worthless.

## "! [rejected] main -> main (non-fast-forward)"

- The remote has commits you do not have. `git pull` first.
- Fetch if you want to inspect: `git fetch` then `git log --oneline HEAD..origin/main`.
- To overwrite deliberately, `git push --force-with-lease` - it refuses if
  someone pushed in between. Plain `--force` will happily delete their work.
- Never force-push `main` or any branch others pull.

## "! [rejected] ... (fetch first)"

- Same cause as above; Git wants the objects before it will push.
- `git pull --rebase` then push.

## "! [rejected] ... (pre-receive hook declined)"

- The server refused: branch protection, a required status check, a signed
  commit rule, a file size limit, or the tag already exists.
- `git push` output usually says why. Force-pushing a branch does not bypass
  protection - you cannot override server policy from the client.
- Push a new tag name, or open a PR, depending on what you intended.

## "You are in 'detached HEAD' state"

- You checked out a SHA or a tag. Nothing is broken.
- `git switch <branch>` to get back onto a branch.
- To keep the work, branch first: `git switch -c <new-name>`. If you switch
  away without branching, the commits become unreachable - recover via
  `git reflog`.

## "! [conflict] Merge conflict in <file>"

- Markers look like `<<<<<<< HEAD` … `=======` … `>>>>>>> branch`.
- Edit each file, delete the markers, `git add <file>`, finish with
  `git commit` (merge) or `git rebase --continue` (rebase).
- Want the whole thing gone: `git merge --abort` / `git rebase --abort`.

## "fatal: You are in the middle of a merge/rebase/cherry-pick"

- You are mid-operation. Finish it or abort it - do not start anything else.
- `git status` explains exactly which step is pending.
- Abort commands: `git merge --abort`, `git rebase --abort`,
  `git cherry-pick --abort`, `git revert --abort`.
- If `.git` looks wrong, `git status` still reads it correctly. Trust it.

## "fatal: not a git repository"

- You are outside the repo, or a parent directory has a `.git` that shadows
  what you expected. `git rev-parse --show-toplevel` shows what Git thinks the
  root is.
- A repo that used to work and now does not: `git config --global --get
safe.directory` - a directory owned by another user needs `git config
--global --add safe.directory <path>`.

## "fatal: detected dubious ownership in repository"

- Another user owns the files. Add the path to `safe.directory` as above.
- Common after extracting a repo into WSL or after a Docker volume mount.

## "error: failed to push some refs" / "Updates were rejected"

- See the non-fast-forward section above. Same problem, different wording.

## "fatal: could not read Username for 'https://github.com'"

- No credential helper or token. `gh auth login` then
  `gh auth setup-git`, or `git config --global credential.helper manager`.
- Wrong account cached: `git credential-manager` / clear via
  `git config --global credential.helper` removal.
- Private repo with HTTPS and no token: switch the remote to SSH.

## "Permission denied (publickey)"

- The key is missing, wrong, or not in the agent. `ssh -vT git@github.com`
  for detail.
- `ssh-add --list` to check the agent, `ssh-add ~/.ssh/id_ed25519` to add.
- Make sure the **public** key is registered on GitHub.

## "fatal: unable to auto-detect email address"

- `git config --global user.email "you@example.com"` (and `user.name`).
- Set a per-repo override with `git config user.email ...` instead if you use
  more than one identity.
- Email privacy: GitHub's noreply address (`<id>+<user>@users.noreply.github.com`)
  works if you enable "Keep my email addresses private".

## "nothing to commit, working tree clean" but you have changes

- The changes are staged already - `git status` shows them under "Changes to
  be committed".
- `git add -A` if the files are untracked but inside an ignored parent.
- If a file is tracked but ignored, `.gitignore` does not apply - use
  `git rm --cached <file>`.

## "warning: LF will be replaced by CRLF"

- Windows line endings. Git normalises to LF in the repo and checks out per
  `core.autocrlf`. Not an error.
- Set `git config --global core.autocrlf input` to store LF and convert on
  commit; or `core.autocrlf false` to leave lines alone.
- A `.gitattributes` with `* text=auto eol=lf` is the durable, per-repo answer
  and beats any global setting. Commit it once and the warning stops for
  everyone.
- Files already committed with mixed endings: `git add --renormalize .`.

## Git is slow

- `git gc` reclaims space; `git maintenance start` schedules it.
- `git count-objects -vH` shows the size of the object store.
- Huge history plus large files: partial clone and sparse checkout from
  [07_large_repos](07_large_repos.md).
- `core.untrackedCache` and `core.fsmonitor` speed up `git status` in large
  trees.
- A network or antivirus scanning `.git` on Windows is a common culprit.

## ".git directory is huge" / repo too big to push

- Something binary got committed. `git count-objects -vH`, then
  `git filter-repo --path <path> --invert-paths` to purge it.
- Push size limits and LFS quota.
- Or accept history and remove only the working-tree copies going forward.

## Recovering Anything

- `git reflog` first. It is the answer to nearly every "I lost it" question.
- `git fsck --lost-found` for dangling objects.
- `git reflog expire --expire=now --all && git gc --prune=now` is the last
  resort, and it makes unrecoverable things genuinely unrecoverable.

## When to Stop and Ask

- A force-push is going to a shared branch.
- You need to rewrite `main` or delete a remote branch.
- A leaked credential is involved - rotate first, clean second.
- You are about to `git clean -fdx` in a repo where you are unsure of the state.
  `git clean -n` previews it.
