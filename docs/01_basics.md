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
