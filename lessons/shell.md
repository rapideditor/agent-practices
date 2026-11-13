# Shell

**zsh doesn't split an unquoted `$var` into words.** `cmd $opts` passes `"-a -b"` as one argument. Use `${=opts}` or an array. (verified: zsh 5.9, 2026-11)

**In zsh, a word starting with `=` expands to a command's path**, so `echo ====` fails with "==== not found". Quote it, or use `printf`. (verified: zsh 5.9, 2026-11)
