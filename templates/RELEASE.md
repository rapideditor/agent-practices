# How to release

1. Run `/release A.B.C` (where `A.B.C` is the new version number) to prepare the CHANGELOG entry and version bump.
2. Review the generated CHANGELOG entry and the `package.json` version bump. Make any corrections.
3. Commit and push: run `/commit`.
4. Create the GitHub release:
   - Go to https://github.com/{owner}/{repo}/releases/new
   - Tag: `vA.B.C` (create new tag on publish)
   - Title: `A.B.C`
   - Body: paste the CHANGELOG section for this release
   - Publish release

<!-- sync:
version=1
source=https://github.com/rapideditor/agent-practices/blob/main/templates/RELEASE.md
-->
