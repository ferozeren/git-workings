GitHub isn't just about storing code; it's a collaborative platform. It offers
features beyond basic version control, such as project management tools like
issue tracking, where you can report bugs and suggest new features. You can
also use GitHub's collaboration features, like pull requests, to propose
changes to a project, fostering community involvement and code review.

- Actions are CI/CD defined in YAML files under `.github/workflows/`. Commits,
  PRs, or a schedule can trigger a run on GitHub-hosted runners.
- Minimal pipeline: `on:` (trigger) → `jobs:` → `runs-on:` → `steps:` with
  `uses:` actions and `run:` shell.
- Learn the two key concepts: the workflow context (`github.*`) and passing
  secrets via `secrets.*` / `env` (never hardcode keys in YAML).
- Useful patterns: `actions/checkout`, `actions/setup-node`, caching
  dependencies with `actions/cache`, and publishing artifacts with
  `actions/upload-artifact`.
- Reusable building blocks live in the Actions Marketplace; you can also call
  private actions from your own repos.
- Check run results on the PR itself, and read the job log when a step fails.

- `gh` is the official terminal client. Install it, then run `gh auth login`
  (choose SSH or HTTPS) and `gh auth status` to confirm.
- Everyday commands: `gh repo clone|create|view`, `gh issue list|create|close`,
  `gh pr create|list|checkout|merge|view`, `gh pr status`,
  `gh run list|watch|rerun`.
- `gh pr create` collects title, body, base and head branches interactively,
  and opens the PR for the branch you are on.
- `gh repo view --web` and `gh browse` jump to the browser. Aliases in
  `~/.config/gh/config.yml` let you wrap long commands.
- Great for scripting anything repetitive, and it complements the web UI and
  the REST/GraphQL API.

- Markdown is what READMEs, issues, PRs, and wikis are written in — worth
  learning properly since it is the documentation layer of every repo.
- Core syntax: headings, **bold**/_italic_, `inline code`, fenced code blocks
  with a language tag, lists, links, images, tables, and blockquotes.
- GitHub extras: task lists (`- [ ]`), tables, footnotes, emoji shortcodes, and
  collapsible `<details>` blocks.
- Apply it well: a good README covers what the project is, install/run
  instructions, usage examples, licence, and contribution notes.
- Add a LICENSE file, CONTRIBUTING.md, and optionally SECURITY.md and GitHub
  Wikis for deeper docs.
