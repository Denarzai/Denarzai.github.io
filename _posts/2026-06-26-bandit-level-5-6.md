---
title: "OverTheWire Bandit: Level 5 to Level 6"
date: 2026-06-26 18:20:00 +0500
categories: [CTF, Bandit]
tags: [linux, find, file-properties, overthewire, beginner]
---

## The Goal

Find a file somewhere under `inhere` that is:
- Human-readable
- Exactly 1033 bytes in size
- Not executable

## What I Did

Navigated into `inhere` and listed the contents:

```bash
bandit5@bandit:~/inhere$ ls
maybehere00  maybehere03  maybehere06  maybehere09  maybehere12  maybehere15  maybehere18
maybehere01  maybehere04  maybehere07  maybehere10  maybehere13  maybehere16  maybehere19
maybehere02  maybehere05  maybehere08  maybehere11  maybehere14  maybehere17
```

Twenty directories. Searching manually wasn't an option. I went to the `find` manual and looked for filters that matched the three properties given. Found `-size` for file size and `! -executable` to exclude executable files:

```bash
bandit5@bandit:~/inhere$ find -size 1033c ! -executable
./maybehere07/.file2
```

One result. Navigated in and read it:

```bash
bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
```

That printed the password.

## Other Ways I Could Have Done It

After solving it I researched other approaches. The full version with all three properties explicitly stated:

```bash
find . -type f -size 1033c ! -executable -readable
```

`-type f` limits results to files only, excluding directories. `-readable` explicitly checks that the file is readable. I got the right answer without these because there was only one 1033-byte non-executable file — but in a messier filesystem these extra filters would matter.

Another approach using `find` piped into `file` to verify human-readability:

```bash
find . -type f -size 1033c ! -executable | xargs file | grep ASCII
```

This takes the find results, runs `file` on each one, and filters for ASCII text. More thorough — confirms human-readable rather than just assuming it.

Using `du` instead of `find`:

```bash
du -b -a | grep 1033
```

`du -b` shows file sizes in bytes, `-a` includes all files not just directories. Piping to `grep 1033` filters for files of that size. Less precise than `find` but another valid route.

## What I Learned

**`find` is a search tool, not just a listing tool.** The real power is in combining filters. A single `find` command can replace what would otherwise be dozens of manual checks.

**`-size` uses unit suffixes** — `c` for bytes, `k` for kilobytes, `M` for megabytes. Writing `1033` without a suffix defaults to 512-byte blocks, which gives the wrong result. The `c` matters.

**`!` negates a condition** — `! -executable` means "not executable." This works with any find filter. `! -name "*.txt"` would exclude txt files, for example.

**`find` flags worth knowing for later levels:**

| Flag | What it does |
|---|---|
| `-size 1033c` | Exactly 1033 bytes |
| `-type f` | Files only |
| `-type d` | Directories only |
| `! -executable` | Not executable |
| `-readable` | Readable by current user |
| `-user bandit7` | Owned by specific user |
| `-group bandit7` | Owned by specific group |
| `-name "*.txt"` | Filename pattern match |

The `-user` and `-group` flags are going to be useful very soon.

## Commands Used

| Command | What it did |
|---|---|
| `ls` | Revealed 20 subdirectories in `inhere` |
| `find -size 1033c ! -executable` | Found the one file matching both properties |
| `cat ./maybehere07/.file2` | Read the password |
