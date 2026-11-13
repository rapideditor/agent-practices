# Lessons

## Structural changes to a template section don't propagate on their own

When a template **changes the structure** of a section (a list collapses to a paragraph, or a section is renamed or reorganized), the sync agent has no diff history to tell it the old shape was replaced on purpose. By default, agents read "this local section differs from the template" as "this is project-specific content, preserve it", so the change silently fails to propagate.

First seen in April 2026, when `## Prompt Files` in `templates/AGENTS.md` went from a list of `/command`s to a single paragraph. Downstream projects synced several times and always kept the old list.

**What to do:** the `AGENTS.md` per-file instructions now say to replace sections whose structure changed, and that differing from the template is *not* a reason to preserve a section. They also name each renamed section by version. When making a similar change to another synced file (`CONTRIBUTING.md`, `RELEASE.md`, …), add the same kind of language to its per-file instructions, or make it a global rule.
