# Git and GitHub Workings

Git is a **free and open source** distributed version control system (something
that tracks changes to files over time) designed to handle everything from
small to very large projects with speed and efficiency.

- Git is a software whereas GitHub is a service.

With a version control system like Git, you can manage your files and projects
much more effectively without the disadvantages of manual versioning. As Git
automatically tracks changes to your files, so you don’t have to create
separate copies every time. This saves time and keeps your work organized.

Instead of wasting storage space with duplicate files, (Conceptually) Git stores
snapshots of project states, with internal compression/delta mechanisms making
storage efficient. Multiple people can work on the same project without
overwriting each other’s changes. It helps keep everything neat and synchronized.

[Git Cheat Sheet](https://git-scm.com/cheat-sheet)

![Git Architecture](https://d8it4huxumps7.cloudfront.net/uploads/images/6465f5b7125e4_what_is_git_1.jpg?d=2000x2000)

## Documentation

1. [01_basics](docs/01_basics.md) Version control concepts, the three states, repositories, staging, commits, resetting, `.gitignore`, branches, and remotes.

2. [02_collaboration](docs/02_collaboration.md) Forking versus cloning, clone options, keeping a branch current, push safety, conflicts, pull requests, and licensing.

3. [03_more_git](docs/03_more_git.md) Rewriting history safely, merge versus rebase versus squash, stashing, and cherry-picking.

4. [04_more_github](docs/04_more_github.md) GitHub Actions, the `gh` CLI, Markdown for repositories, and project files worth having.

5. [advanced/](docs/advanced/README.md) Undo and recovery, commit quality and history rewriting, large repositories, hooks and automation, security, and troubleshooting.

## Git Configuration

1. `git config --global user.name "<name>"` Sets your name for Git commits.

2. `git config --global user.email "<email>"` Sets your email for Git commits.

3. `git config --global <key> <value>` Sets a configuration value for Git.

4. `git config --list` Lists all configuration values for Git.

5. `git config --global init.defaultBranch main` Names the branch created by `git init`, so new repositories start on `main` instead of `master`.

6. `git config --global pull.rebase true` Makes `git pull` rebase by default, keeping a linear history instead of creating merge commits.

7. `git config --global core.hooksPath .githooks` Points Git at a committed hooks directory so hooks are shared instead of living only in `.git/hooks`.

## Common Git Commands

1. `git init` Starts a new Git repository in the current folder so Git can track changes.

2. `git add <filename...>` Adds files from your working directory to the index so they can be included in the next commit.

3. `git commit -m <message>` Adds files from the index to the object store with a message describing what you changed.

4. `git mv <source-file> <destination-file>` Renames a file in both the working directory and the index.

5. `git rm <file>` Remove a file from both the working directory and the index (staging area).

6. `git rm --cached <file>` Remove a file from the index (staging area) but not from the working directory.

7. `git status` Show the current state of the working directory and index.

8. `git clone <repository-url>` Clones a remote repository to your local machine. It automatically creates a new directory with the repository name and sets up the remote tracking branches.

9. `git diff` Show changes between the working directory and the index.

10. `git diff --cached` Show changes between the index and the last commit.

11. `git show <commit-id>` Shows the details of a specific commit, including the author, date, message, and code changes. Great for reviewing what was changed in that commit.

12. `git log` Shows a list of past commits in your project, including commit IDs, authors, dates, and messages.

13. `git restore <file>` Copies the file from the staging area (index) to your working directory, discarding local changes.

14. `git restore --staged <file>` Copies the file from the last commit to the staging area (index), unstaging it.

15. `git branch <branch-name>` Creates a new branch with the given name.

16. `git switch <branch-name>` Switches to the specified branch (recommended for newer Git versions).

17. `git checkout <branch-name>` Also switches to the specified branch (older alternative to switch).

18. `git branch` Lists all branches in your repository and highlights the current one.

19. `git merge <branch-name> -m <message>` Merges the specified branch into your current one and adds a custom commit message.

20. `git merge --abort` Stops a merge and resets everything back to how it was before the merge started.

21. `git reset --soft <commit_id>` Moves HEAD to the given commit, making it the new last commit, but leaves the index and working directory unchanged.

22. `git reset --mixed <commit_id>` Moves HEAD to the given commit and copies its files to the index, discarding old staged changes but keeping changes in the working directory.

23. `git reset --hard <commit_id>` Moves HEAD to the given commit and copies its files to both the index and working directory, discarding all local changes completely.

24. `git pull` Fetches changes from the remote repository and integrates them( merge or rebase, depending on the configuration) into your current branch.

25. `git push` Uploads your local commits to the remote branch it tracks.

26. `git push -u origin <branch-name>` Pushes and sets the upstream tracking branch, so later pushes are just `git push`.

27. `git push --force-with-lease` Overwrites a remote branch, but refuses if someone else pushed since your last fetch. Safer than `--force`.

28. `git remote -v` Lists the configured remotes and their URLs. Update one with `git remote set-url origin <url>`.

29. `git tag <tag-name>` Creates a lightweight tag at the current commit; `git tag -a <tag-name> -m "<message>"` creates an annotated tag worth pushing.

30. `git stash` Shelves your uncommitted changes to give you a clean working tree. See [docs/03_more_git](docs/03_more_git.md).

31. `git rebase <branch-name>` Replays your commits on top of another branch under new commit IDs, producing a linear history.

32. `git revert <commit-id>` Creates a new commit that undoes an earlier one, keeping already-pushed history intact.

33. `git cherry-pick <commit-id>` Applies the changes from one specific commit onto your current branch.

34. `git bisect` Narrows down the commit that introduced a bug by testing one midpoint at a time. `git bisect reset` returns to where you started.

35. `git reflog` Lists every position `HEAD` has held. The way back from a bad `reset`, a deleted branch, or a lost commit.

36. `git worktree add <path> <branch-name>` Creates a second working directory for the same repo, so two branches can be checked out at once.

37. `git submodule add <url> <path>` Adds another repository tracked inside this one. See [docs/advanced/](docs/advanced/README.md).

## Resources

- [Pro Git](https://git-scm.com/book/en/v2) The reference book, free online.
- [GitHub Docs](https://docs.github.com) Guides for repositories, Actions, and the REST API.
- [Oh Shit, Git!?!](https://ohshitgit.com) Recovery from common mistakes, searched by error message.
- [Git Cheat Sheet](https://git-scm.com/cheat-sheet) One-page command reference.
