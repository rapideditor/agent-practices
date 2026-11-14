# Git

**These repos merge PRs with rebase-merge, which rewrites commit hashes.** After a PR merges, the local branch's commits don't match `main`'s. Fetch, check there's nothing local you still need, then reset `main` to `origin/main`. (verified: 2026-11)

**Before branching, check for unpushed commits on `main`** (`git log origin/main..main`). The maintainer sometimes commits small fixes straight to `main`. Push them, or they end up in the new branch's PR. (verified: 2026-11)

**`git branch -r` keeps listing branches already deleted on GitHub until you run `git fetch --prune`.** Before reporting or deleting remote branches, prune first, or ask the remote directly with `git ls-remote --heads origin`. GitHub's "automatically delete head branches" setting removes merged PR branches on the remote, but local clones still show them. (verified: git 2.x, GitHub, 2026-11)

**Write commit messages to a file with `printf` and use `git commit -F`.** `-m` with multi-line text and heredocs both break easily in agents' shell tools. The `/commit` skill does this. (verified: 2026-11)
