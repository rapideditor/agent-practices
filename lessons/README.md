# Lessons

Gotchas and facts about the tools we use, collected from real work across projects. Each one cost someone time once; reading it should save the next agent that time.

Lessons aren't synced into downstream projects. An agent fetches this index when it's relevant (before working with one of these tools in an unfamiliar way, or when something behaves unexpectedly), then reads only the files it needs. Links below are relative, so fetch them from the same base URL as this file.

## Index

| File | Covers |
|------|--------|
| [bun.md](bun.md) | Bun runtime, package manager, scripts and shell |
| [typescript.md](typescript.md) | TypeScript compiler options and type patterns |
| [packaging.md](packaging.md) | Publishing to npm: `exports`, `engines`, package linting |
| [git.md](git.md) | Git and GitHub workflow in these repos |
| [shell.md](shell.md) | Shell quirks agents run into (zsh on macOS) |

## Writing a lesson

- Add it to the file for its topic, or start a new file and add it to the index.
- Lead with the fact in bold, in one sentence. Then a line or two on why it matters or what to do instead.
- End with what it was checked against: `(verified: Bun 1.4.4, 2026-11)`. A later agent can then tell when to re-check it.
- Only general lessons belong here. Something specific to one project goes in that project's own notes.
- When a lesson turns out to be wrong or out of date, fix or delete it.

These files are public. Link only to public PRs and issues, and keep out anything private: secrets, personal details, unreleased plans, or anything about specific people.
