# Git and GitHub Workings

Git is a **free and open source** distributed version control system (something
that tracks changes to files over time) designed to handle everything from
small to very large projects with speed and efficiency.

- Git is a software whereas GitHub is a service.

With a version control system like Git, you can manage your files and projects
much more effectively without the disadvantages of manual versioning. As Git
automatically tracks changes to your files, so you don’t have to create
separate copies every time. This saves time and keeps your work organized.

Instead of wasting storage space with duplicate files, Git only stores the
changes, making it efficient for small or large projects. Multiple people can
work on the same project without overwriting each other’s changes. It helps
keep everything neat and synchronized.

[Git Cheat Sheet](https://git-scm.com/cheat-sheet)

## Common Git Commands

1. `git init` Starts a new Git repository in the current folder so Git can track changes.

2. `git add <filename...>` Adds files from your working directory to the index so they can be included in the next commit.

3. `git commit -m <message>` Adds files from the index to the object store with a message describing what you changed.

4. `git mv <source-file> <destination-file>` Renames a file in both the working directory and the index.

5. `git rm <file>` Remove a file from both the working directory and the index (staging area).

6. `git rm --cache <file>` Remove a file from the index (staging area) but not from the working directory.

7. `git status` Show the current state of the working directory and index.

8. `git diff` Show changes between the working directory and the index.

9. `git diff --cached` Show changes between the index and the last commit.

10. `git show <commit-id>` Shows the details of a specific commit, including the author, date, message, and code changes. Great for reviewing what was changed in that commit.

11. `git log` Shows a list of past commits in your project, including commit IDs, authors, dates, and messages.

12. `git restore <file>` Copies the file from the staging area (index) to your working directory, discarding local changes.

13. `git restore --staged <file>` Copies the file from the last commit to the staging area (index), unstaging it.

14. `git branch <branch-name>` Creates a new branch with the given name.

15. `git switch <branch-name>` Switches to the specified branch (recommended for newer Git versions).

16. `git checkout <branch-name>` Also switches to the specified branch (older alternative to switch).

17. `git branch` Lists all branches in your repository and highlights the current one.

18. `git merge <branch-name> -m <message>` Merges the specified branch into your current one and adds a custom commit message.

19. `git merge --abort` Stops a merge and resets everything back to how it was before the merge started.

20. `git reset --soft <commit_id>` Moves HEAD to the given commit, making it the new last commit, but leaves the index and working directory unchanged.

21. `git reset --mixed <commit_id>` Moves HEAD to the given commit and copies its files to the index, discarding old staged changes but keeping changes in the working directory.

22. `git reset --hard <commit_id>` Moves HEAD to the given commit and copies its files to both the index and working directory, discarding all local changes completely.

![Git Architecture](https://d8it4huxumps7.cloudfront.net/uploads/images/6465f5b7125e4_what_is_git_1.jpg?d=2000x2000)
