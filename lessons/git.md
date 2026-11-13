# Git

**These repos merge PRs with rebase-merge, which rewrites commit hashes.** After a PR merges, the local branch's commits don't match `main`'s. Fetch, check there's nothing local you still need, then reset `main` to `origin/main`. (verified: 2026-11)

**Before branching, check for unpushed commits on `main`** (`git log origin/main..main`). The maintainer sometimes commits small fixes straight to `main`. Push them, or they end up in the new branch's PR. (verified: 2026-11)

**Write commit messages to a file with `printf` and use `git commit -F`.** `-m` with multi-line text and heredocs both break easily in agents' shell tools. The `/commit` skill does this. (verified: 2026-11)
