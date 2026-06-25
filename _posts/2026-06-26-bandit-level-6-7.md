---
title: "OverTheWire Bandit: Level 6 to Level 7"
date: 2026-06-26 18:40:00 +0500
categories: [CTF, Bandit]
tags: [linux, find, permissions, stderr, redirect, overthewire, beginner]
---

## The Goal

Find a file stored somewhere on the entire server with these properties:
- Owned by user `bandit7`
- Owned by group `bandit6`
- 33 bytes in size

## What I Did

I knew the filters from the previous level — `-user`, `-group`, `-size`. I started searching from `/home` since that's where all the bandit users live:

```bash
bandit6@bandit:/home$ find -user bandit7 -group bandit6 -size 33c
```

Nothing. Just Permission denied errors. I tried adding and removing filters, combining them differently — same result every time. The filters were correct but I wasn't finding anything.

The issue took me a while to realise: I was searching from `/home` thinking that covered the whole system. It doesn't. `/home` is just one directory. The file could be anywhere on the server.

I switched to `/` to search from the root of the entire filesystem:

```bash
bandit6@bandit:/home$ find / -user bandit7 -size 33c -group bandit6
```

Now results appeared — buried inside hundreds of Permission denied errors. I could see the answer in there:

```
/var/lib/dpkg/info/bandit7.password
```

But the output was too messy to work with cleanly. I added `2>/dev/null` to silence the errors:

```bash
bandit6@bandit:/home$ find / -user bandit7 -size 33c -group bandit6 2>/dev/null
/var/lib/dpkg/info/bandit7.password
```

One clean result. Read it:

```bash
bandit6@bandit:/var/lib/dpkg/info$ cat bandit7.password
```

That printed the password.

## What Was Actually Happening

**The wrong starting directory** was the core mistake. When you run `find` without a path it searches the current directory. I was in `/home`, so all my correct filters were searching in the wrong place. The file was at `/var/lib/dpkg/info/bandit7.password` — completely outside `/home`. Switching to `/` searches the entire filesystem from the root down.

**The Permission denied errors** are stderr — the error output stream. Every command has three streams:

- **stdin (0)** — input coming in
- **stdout (1)** — normal output going out  
- **stderr (2)** — error messages going out

By default both stdout and stderr print to the terminal mixed together. The actual result — the file path — went to stdout. The Permission denied lines went to stderr. `2>/dev/null` redirects stream 2 (stderr) to `/dev/null` — a special file that silently discards everything written to it. Errors disappear. Only the real result remains.

## Other Ways to Handle the Output

**Keep errors, send results to a file:**
```bash
find / -user bandit7 -group bandit6 -size 33c 1>results.txt 2>/dev/null
```

**Send both to separate files:**
```bash
find / -user bandit7 -group bandit6 -size 33c 1>results.txt 2>errors.txt
```

**Send both to the same file:**
```bash
find / -user bandit7 -group bandit6 -size 33c 2>&1 | grep -v "Permission denied"
```
`2>&1` merges stderr into stdout, then `grep -v` filters out lines containing "Permission denied".

## What I Learned

**Always check where you're searching from.** `find` without a path searches the current directory only. If the file could be anywhere on the system, start from `/`.

**`/dev/null` is a discard bin.** Anything redirected there disappears permanently and silently. It's the standard way to suppress output you don't care about.

**stderr and stdout are separate streams.** The number matters — `2>` redirects errors specifically. `1>` redirects normal output. Understanding the difference lets you filter exactly what you want to see.

## Commands Used

| Command | What it did |
|---|---|
| `find -user bandit7 -group bandit6 -size 33c` | Correct filters, wrong directory — searched `/home` only |
| `find / -user bandit7 -size 33c -group bandit6` | Searched whole system — found the file but noisy output |
| `find / -user bandit7 -size 33c -group bandit6 2>/dev/null` | Clean result — errors silenced |
| `cat bandit7.password` | Read the password |
