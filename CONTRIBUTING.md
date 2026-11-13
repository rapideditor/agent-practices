# Contributing

This project uses **GitHub** to track issues and manage our source code.
- Check out the [Git Guides](https://github.com/git-guides) to learn more.

This project uses **Markdown**.
- Check out the [Markdown Guide](https://www.markdownguide.org/).

This project uses **Bun** as our development environment.
- Check out the [Bun Docs](https://bun.com/docs) to learn more.
- (It's similar to other JavaScript tools like Node/Jest/Esbuild/Vite, so knowing any of those already will help you a lot).
- Bun supports both JavaScript and TypeScript.

If you want to contribute to agent-practices, you'll probably need to:
- [Install Bun](https://bun.com/docs/installation)
- `git clone` agent-practices
- `cd` into the project folder
- `bun install` the dependencies

Try to keep things simple!

## Making changes

- Everything synced to downstream projects lives in `templates/`. Improve shared files here first, then `/sync` them down — don't improve them locally in a downstream project.
- When you make a meaningful change to a file in `templates/`, bump its version in [`templates/.sync.manifest.md`](templates/.sync.manifest.md). Cosmetic changes (typos, whitespace) don't need a bump.
- When you add a file, add it to **both** the version table and the per-file instructions in the manifest. When it's a skill, also add it to the Skills table in [`README.md`](README.md#skills).
- In a skill's YAML frontmatter, put values in double quotes if they contain `: ` or ` #`. Unquoted, `: ` is a YAML error and ` #` starts a comment that cuts off the rest of the line. Claude Code is forgiving about this, but other hosts may not be.
- Bumping `.claude/skills/sync/SKILL.md` makes every downstream project's next `/sync` stop after Phase 1 and ask to be re-run, so batch sync skill changes where you can.
- Some root files are symlinks into `templates/` (`AGENTS.md`, `.editorconfig`, `.claude/skills/`). `.gitignore` and `.gitattributes` are copies instead, because Git won't read them through a symlink. `knowledge/README.md` is a copy too, so GitHub shows it when you open the folder. Keep them identical to their templates.
- `playbooks/` and `lessons/` aren't synced or versioned, so changes there need no manifest bump. Follow the format in each folder's `README.md`, keep its index up to date, and update an entry's "verified" note when you re-check it.
- Everything in this repo is public. Keep private details out of playbooks and lessons, as each folder's `README.md` describes.

## AI-Assisted Contributions

We welcome contributions made with the help of AI tools.
If you use them, you are responsible for understanding and reviewing the output before submitting it.
Generated code, issues, and PR descriptions should be clear and relevant — not verbose for the sake of it.
