# Git and GitHub

## Version Control

Version control is a system that tracks changes to files over time, so
developers can see who changed what and revert to earlier versions when needed.
It keeps a history of every modification, letting multiple people work on the
same project without overwriting each other's work.
Teams use it to collaborate safely, experiment with new features in isolation,
and recover from mistakes.

- A VCS records meaningful snapshots over time — what changed, who changed it, why.
- Git is _distributed_: every clone has the full history, so offline work works and there is no single point of failure.
- Contrast SVN/CVS: those are _centralized_, which makes offline work and branching awkward.
- The three states to memorise: **working directory → staging area → repository**.

- Git is a **distributed** VCS. Everyone gets a full clone with complete
  history, so you can commit offline and there is no single point of failure.
- Contrast: SVN and CVS are **centralized** — history lives on one server, so
  offline work and branching are awkward.
- Know the three states: working directory → staging area → repository (commit).

- A repository is a project directory plus a `.git` folder holding the full
  history, branches, and config.
- `git init` inside a project folder creates that `.git` directory and turns it
  into a repo. `git init <dir>` creates the dir too.
- The default branch may be `master` or `main` depending on version/config; set
  `git config --global init.defaultBranch main`.
- You can turn an existing project into a repo at any time without losing
  anything.
- A repository is local until you add a remote.

- Working directory → staging area → repository. Learn which command moves what.
- The three states are not decoration; they are what make reviewable commits
  possible.
- Stashing is for emergency clean-tree needs,
  not long-term storage.
- Reset vs revert: `reset` rewrites, `revert` adds a new commit

- The three places a file can live: working directory → staging area →
  repository (committed history).
- `git add <file>` stages one file; `git add .` or `git add -A` stages
  everything in the current directory.
- Staging is what lets you commit a coherent, reviewable slice of work rather
  than every unrelated change on your disk.
- Inspect before committing: `git status` (short), `git diff` (unstaged),
  `git diff --staged` (staged).
- Unstage with `git restore --staged <file>` (or `git reset <file>` in older
  versions).

- `git commit -m "message"` records the staged snapshot with an author,
  timestamp, and parent pointer.
- Write good messages: imperative mood ("Add login form", not "Added"), short
  summary line (≤50 chars), blank line, then a body explaining _why_.
- Never commit secrets, build output, or large binaries.
- Amend your most recent commit: `git commit --amend --no-edit`.
- Review history with `git log --oneline --graph --decorate --all`; inspect a
  single commit with `git show <sha>`.

- `git reset <file>` moves a file out of the staging area back to unstaged,
  keeping your edits. This is the everyday beginner use.
- `git reset --soft <sha>` moves HEAD back but keeps changes staged.
- `git reset --mixed <sha>` (default) moves HEAD back and unstages changes.
- `git reset --hard <sha>` also throws away working-tree changes. Destructive —
  reach for it only when you are sure.
- Safest "undo" for something already pushed is `git revert <sha>`, which
  records a new commit rather than rewriting history.

- `.gitignore` lives in the repo root and applies recursively to its
  directory; later rules override earlier ones.
- Committing a file that is already tracked ignores nothing — you must
  `git rm --cached <file>` first.
- Use the official template at github.com/github/gitignore.
- Never commit secrets or API keys; prefer environment variables.

- `git branch` lists branches; `git branch <name>` creates one; `git switch
<name>` moves between them.
- `git switch -c <name>` creates and switches in one step (`git checkout -b` is
  the older form).
- Rename: `git branch -m <new>`. Delete: `git branch -d <name>` (safe, refuses
  unmerged) or `-D` (force).
- Merge: `git switch main` then `git merge <branch>`. A fast-forward merge just
  slides the label; a non-fast-forward creates a merge commit.
- Branches that touch the same lines will conflict

- On GitHub, "New repository" can be created from scratch, or by importing an
  existing local project.
- Choose visibility deliberately: private repos need a paid plan once you
  exceed the free private quota.
- Add a README, `.gitignore`, and a licence at creation time — it makes the
  first push cleaner.
- Protect the default branch (require reviews / status checks) once others can
  push.

- A remote is a named URL: `git remote add origin <ssh-or-https-url>`, list
  with `git remote -v`.
- First push: `git push -u origin main`. The `-u` sets upstream so later pushes
  are just `git push`.
- `git fetch origin` downloads new commits **without** changing your working
  tree — always safe.
- `git pull` = fetch + integrate (merge or rebase). `git pull --rebase` keeps
  history linear.
- Update the remote URL with `git remote set-url origin <url>`; rename with
  `git remote rename`.
