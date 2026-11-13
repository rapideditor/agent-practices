# Quirks

- **Git (2.32+) won't read `.gitignore` or `.gitattributes` through a symlink** ("Too many levels of symbolic links"). That's why those two root files are copies of their templates, and can drift. After editing either template, check `diff .gitignore templates/.gitignore`, and the same for `.gitattributes`. `knowledge/README.md` is also a copy, so GitHub shows it when you open the folder.
- **raw.githubusercontent.com caches files for about 5 minutes.** A `/sync` right after a push may fetch a mix of old and new files.
