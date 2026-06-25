---
title: "OverTheWire Bandit: Level 4 to Level 5"
date: 2026-06-26 18:00:00 +0500
categories: [CTF, Bandit]
tags: [linux, file, wildcards, overthewire, beginner]
---

## The Goal

The password is stored in the only human-readable file inside the `inhere` directory.
There are ten files. I need to find which one is readable without opening each one manually.

## What I Did

Navigated into `inhere` and listed the files:

```bash
bandit4@bandit:~$ cd inhere
bandit4@bandit:~/inhere$ ls
-file00  -file01  -file02  -file03  -file04  -file05  -file06  -file07  -file08  -file09
```

Ten files all starting with `-`. I knew I needed the `file` command to check what type each one was without opening them. My first attempt:

```bash
bandit4@bandit:~/inhere$ file *
file: Cannot open `ile00' (No such file or directory)
```

That failed in an interesting way. The shell expanded `*` into `-file00 -file01 -file02...` and `file` saw `-file00` as a flag. It consumed `-f` as a flag character and was left trying to open `ile00` — which doesn't exist. The `-f` disappeared because the command interpreted it as an option.

I tried quoting the wildcard:

```bash
bandit4@bandit:~/inhere$ file "*"
*: cannot open `*' (No such file or directory)
```

That didn't expand at all — quotes prevented the shell from treating `*` as a wildcard so it looked for a literal file called `*`.

Same fix as the dashed filename levels — prefix with `./`:

```bash
bandit4@bandit:~/inhere$ file ./*
./-file00: data
./-file01: data
./-file02: OpenPGP Secret Key
./-file03: data
./-file04: data
./-file05: data
./-file06: Non-ISO extended-ASCII text, with NEL line terminators
./-file07: ASCII text
./-file08: data
./-file09: data
```

`-file07` is the only one showing `ASCII text` — that's the human-readable one. Everything else is binary data or other formats.

```bash
bandit4@bandit:~/inhere$ cat ./-file07
```

That printed the password.

I also tried `cat ./--file07` by mistake — double dash didn't work. The filename only has one dash.

## What Was Actually Happening

**`file` identifies file types** without opening them. It reads the first few bytes of a file — called the magic bytes — and matches them against a database of known file signatures. `ASCII text` means the file contains readable characters. `data` means binary content — not human readable.

**`*` wildcard expansion** happens before the command runs. The shell replaces `*` with all matching filenames first, then passes the result to the command. So `file *` becomes `file -file00 -file01...` before `file` ever sees it. The command then misreads the leading dashes as flags.

**`./` fixes it again** for the same reason as before — it turns bare names into explicit paths, so `-file00` becomes `./-file00` and the command can't mistake it for a flag.

## What I Learned

**`file ./*` is the pattern to use** whenever you need to run a command on multiple files that start with `-`. The `./` prefix propagates to every filename the wildcard expands to.

**Magic bytes identify file types** — the `file` command doesn't rely on extensions. A file called `document.txt` containing binary data would still be identified as `data`. Extensions are just hints for humans, not the OS.

**Wildcards expand before execution** — understanding when the shell processes something versus when the command processes it is important. The shell handles `*` expansion. The command never sees the `*` at all.

## Commands Used

| Command | What it did |
|---|---|
| `ls` | Listed the ten files in `inhere` |
| `file *` | Failed — shell expanded to dashed names, misread as flags |
| `file "*"` | Failed — quotes prevented wildcard expansion entirely |
| `file ./*` | Worked — explicit paths prevented flag misinterpretation |
| `cat ./-file07` | Read the only ASCII text file |
