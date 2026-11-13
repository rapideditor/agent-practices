# Playbooks

Step-by-step guides for one-time upgrades that many projects go through: switching a tool, moving to a new major version, changing how a package is shipped. They're written for agents, and come from doing the upgrade for real in at least one project.

Playbooks aren't synced into downstream projects. An agent fetches this index when it's relevant (before an upgrade, or during `/checkup`), then reads only the playbooks that apply. Links below are relative, so fetch them from the same base URL as this file.

## Index

| Playbook | Applies when |
|----------|--------------|
| [ESLint → oxlint](eslint-to-oxlint.md) | `eslint` is in `devDependencies` |
| [TypeScript 7](typescript-7.md) | `typescript` is below 7 |
| [ESM only](esm-only.md) | A published package still ships a CommonJS build |
| [Package checks](package-checks.md) | A published package doesn't run publint and arethetypeswrong |

## Using a playbook

- Check **Applies when** against the project first. Some playbooks have an order (oxlint before TypeScript 7).
- Treat the steps as a starting point. The project's own conventions (in its `AGENTS.md`) win, and drift is fine.
- Check **Last verified**. If the tools have moved on a lot since then, check their docs before trusting version-specific details.
- Run the **Verify** steps at the end.
- If a step was wrong or out of date, say so, and offer to update the playbook here (see `/reflect`).

## Writing a playbook

One file per upgrade, named for what it does. Use these sections:

- A one-line summary under the title, then **Applies when** and **Last verified** (tool versions, month, and a link to the PR where it was done).
- **Why**: what the project gains, briefly.
- **Steps**: numbered, concrete, with the commands and config.
- **Gotchas**: what went wrong or was surprising last time.
- **Verify**: the commands that show it worked.

These files are public. Link only to public PRs and issues, and keep out anything private: secrets, personal details, unreleased plans, or anything about specific people.
