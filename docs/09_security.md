# Security

Git is a distributed store of everything you have ever committed. Two habits
carry most of the risk: how credentials reach the remote, and what you commit.

## Remotes and Credentials

- Prefer **SSH** over HTTPS for GitHub. It authenticates once, per key, and
  never asks for a token.
- **HTTPS** works out of the box with the GitHub CLI or credential manager:
  `git config --global credential.helper manager` on Windows (Git
  Credential Manager), `osxkeychain` on macOS, `cache` on Linux.
- With HTTPS + a PAT, the token replaces your password. Use a fine-grained token
  scoped to the one repo - never a classic `repo`-wide token.
- `gh auth setup-git` rewrites remotes to use `gh` as the credential helper, so
  `gh auth login` is the only login you need.
- `git remote -v` will happily show a token if one got pasted into the URL.
  Rewrite with `git remote set-url origin <clean-url>`.
- A remote URL is public configuration. Do not put tokens in it.

## SSH Keys

- Generate an Ed25519 key: `ssh-keygen -t ed25519 -C "you@example.com"`.
- Add the **public** key (`~/.ssh/id_ed25519.pub`) to GitHub under Settings →
  SSH and GPG keys. Never share the private key.
- `~/.ssh/config` picks the key per host:

```
Host github.com
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
```

- `ssh -T git@github.com` verifies it. A permission denied message usually
  means the key is not in the agent (`ssh-add --apple-use-keychain`).
- SSH is permissive by default. Lock it down in `~/.ssh/config` with
  `Host *` / `StrictHostKeyChecking yes` and `UpdateHostKeys yes`, and set
  `PasswordAuthentication no` if you use keys everywhere.
- Use `~/.ssh/config` per-workspace entries instead of dropping key files into
  each project.
- Rotate: generate a new key, add it, then remove the old one from GitHub.
  Periodically, and immediately after any machine you do not control.

## Committing Secrets

- `.gitignore` first: `.env`, `.env.*`, `*.pem`, `*.key`, `id_rsa`, plus your
  cloud credential directories (`~/.aws`, `~/.config/gcloud`).
- Committing a file already tracked ignores `.gitignore`. Remove it first:
  `git rm --cached <file>`.
- Prefer environment variables and a secret manager (Vault, AWS/GCP secret
  stores, 1Password) over `.env` files checked into the repo.
- GitHub pushes secret scanning on public repos automatically and scans partner
  forks of private repos. It does not save you - assume prevention.
- Pre-commit hooks can block obvious leaks before they commit; `pre-commit`
  with `detect-secrets` or `gitleaks` is the common setup.
- A `*.example` file documents the shape of a config without its contents.

## Removing a Leaked Secret

Assume a leaked credential is **burned**: revoke or rotate it first, then clean
the history. Order matters.

- Rotate/revoke through the provider first. Removing the commit does not
  un-leak it - forks and clones may already have it.
- `git filter-repo --path config/secrets.yml --invert-paths` (install via
  `pipx install git-filter-repo`) is the right tool.
- Removing a specific string across history:
  `git filter-repo --replace-text expressions.txt`, one literal per line.
- For history rewriting you must force-push, so: back up, notify collaborators,
  then force-push every branch and tag.
- `git push --force --mirror` covers branches and tags together. Prefer
  `--force-with-lease` where you can.
- Then ask GitHub Support to purge the cached views of dangling commits, and
  fork owners to re-sync.
- `git filter-branch` still exists but is deprecated. Avoid.
- `.git` keeps dangling objects until expiry. On a local clone you can
  `git reflog expire --expire=now --all` then `git gc --prune=now`.
- After any rewrite, `git log --all -- <path>` should show nothing.

## Signing and Verifying

- Signing proves authorship. It does not keep the secret out of your history -
  those are separate problems.
- SSH signing is the low-friction option; `git config --global gpg.format ssh`
  and `user.signingkey ~/.ssh/id_ed25519.pub`.
- With GPG: `git config --global user.signingkey <key-id>` and
  `commit.gpgsign true`, then `git config --global gpg.program` if `gpg` is not
  on PATH (macOS especially).
- `git config --global tag.gpgsign true` to sign tags, which is what
  verifiable releases need.
- Verify with `git log --show-signature`; GitHub shows a "Verified" badge once
  the key is added as a GPG key or SSH signing key.
- Tell users to verify signatures on downloaded releases, not just on code.

## Access Control on GitHub

- Branch protection on `main`: require reviews, require status checks, require
  the branch to be up to date, and disforce direct pushes.
- Require signed commits for the protected branch once your team signs them.
- Fine-grained PATs and read-only deploy keys are safer than a broad token.
- Least privilege by default: contributors fork, maintainers branch-protect,
  secrets are scoped per-environment.
- `.github/workflows` on `pull_request` runs with a read-only token on
  forks. Keep it that way; use `pull_request_target` only with care.
- Dependabot, code scanning and secret scanning are free on public repos and
  often free on private ones - turn them on.

## Repository Hygiene

- A `.gitignore` reviewed on day one saves archaeology later.
- Never commit `.env`, build artefacts, `node_modules/`, `dist/`, editor
  config, or large binaries.
- Licence and `SECURITY.md` tell people how to report a vulnerability
  privately, instead of opening a public issue.
- Review the collaborator list and deploy keys periodically; remove anyone who
  left.
