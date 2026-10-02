Collaboration in Git and GitHub refers to the process of multiple people working together on the same project,
managing changes, and integrating their contributions seamlessly. It involves using Git's branching and merging
capabilities, along with GitHub's pull request workflow, to coordinate efforts, review code, and ultimately
build a cohesive and functional project.

- A **fork** is a server-side copy of someone else's repo into your account. It
  is how you contribute to a project you do not have write access to.
- Forking is a GitHub-web concept (and how open source works). Cloning is the
  local copy you make with `git clone`.
- Typical OSS flow: fork → clone your fork → branch → commit → push to your fork
  → open a PR back to the original repo (upstream).
- You can sync your fork with upstream later by adding upstream as a second
  remote and pulling.

- `git clone <url>` downloads a full copy including all history, into a folder
  named after the repo.
- Clone over SSH (`git@github.com:user/repo.git`) if you set up a key; over
  HTTPS (`https://github.com/user/repo.git`) if not.
- Shallow clone for speed: `git clone --depth 1 <url>`. Clone a single branch:
  `git clone --branch <name> --single-branch <url>`.
- You can clone into an existing directory with `git clone <url> .`
- A clone gives you an `origin` remote pointing at the source, ready to push.

- Forking is server-side on GitHub; cloning is a local copy with `git clone`.
  They solve different problems.
- The single most useful habit: `git fetch` before you start work. It is always
  safe and tells you what you are about to deal with.
- Conflicts are normal, not failure.
- A repo with no licence is legally unusable by others.

- A PR proposes merging your branch into another. Open it from the "Compare &
  pull request" link after pushing, or via Pull requests → New pull request.
- Write a good title and body: what changed, why, and how you tested. Link the
  issue with "Closes #123".
- Keep PRs small and single-purpose — easier to review and faster to merge.
- Request reviewers, assign yourself, add labels, and turn on draft mode if it
  is not ready.
- Respond to review comments by pushing follow-up commits; the PR diff updates
  automatically.
- Use saved replies for recurring comments, and @mentions to pull in people.

- Keep your branch current before you start work: `git switch <branch>` then
  `git pull`.
- `git fetch origin` updates your remote-tracking refs only — safe,
  non-destructive, no merge.
- `git pull` = fetch + integrate. Choose merge or rebase explicitly
  (`git pull --rebase`) so you are not surprised.
- If a branch diverged, you will need to merge or rebase before pushing; Git
  will refuse a non-fast-forward push.
- Never force-push shared branches. `git push --force-with-lease` is the safer
  variant for your own feature branch.

- A conflict happens when two commits change the same lines. Git pauses and
  marks the file with `<<<<<<<`, `=======`, `>>>>>>>` markers.
- Process: open each conflicted file → decide which version wins (or combine
  both) → delete the markers → `git add <file>` → `git commit`.
- Abort a merge with `git merge --abort` and redo it.
- Reduce future conflicts: rebase on `main` frequently, keep branches
  short-lived, and make small focused commits.

- The licence tells others what they may legally do with your code. Missing
  licence = all rights reserved by default, so it blocks real adoption.
- Add a LICENSE file at the repo root and reference it in the README. Also
  learn CONTRIBUTING.md, CODE_OF_CONDUCT.md, and SECURITY.md.
