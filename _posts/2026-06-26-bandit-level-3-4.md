---
title: "OverTheWire Bandit: Level 3 → Level 4"
date: 2026-06-26 14:00:00 +0500
categories: [CTF, Bandit]
tags: [linux, hidden-files, find, ls, overthewire, beginner]
---

## The Goal

The password is stored in a hidden file inside the `inhere` directory.

## What I Did

Logged in as `bandit3`, confirmed the `inhere` directory and navigated into it:

```bash
bandit3@bandit:~$ ls
inhere
bandit3@bandit:~$ cd inhere
bandit3@bandit:~/inhere$ ls
```

`ls` returned nothing. The directory looked empty. I knew the file had to be there somewhere so I ran `find`:

```bash
bandit3@bandit:~/inhere$ find
.
./...Hiding-From-You
```

There it was. I tried `cd` into it out of habit, which failed as expected — it's a file not a directory. Then read it the same way as the dashed filename from Level 1:

```bash
bandit3@bandit:~/inhere$ cat "./...Hiding-From-You"
```

That printed the password.

## What Was Actually Happening

In Linux, any file or directory whose name starts with a `.` is hidden from `ls` by default. It's a convention — not real security — designed to keep config files and system files out of the way so they don't clutter your view. The file was always there. `ls` just wasn't showing it.

There are two ways to see hidden files:

```bash
ls -a
```

The `-a` flag tells `ls` to show all files including hidden ones. This would have shown `...Hiding-From-You` directly.

`find` works too — it searches recursively and lists everything it finds regardless of the dot convention. Running it with no arguments searches the current directory and prints every path including hidden files.

## What I Was Confused About

I wasn't sure what "hidden" actually meant in Linux. I assumed it might involve some kind of access restriction or encryption. It turned out to be much simpler — just a naming convention. A dot at the start of the filename is all it takes to hide something from a basic `ls`.

I also tried `cd` into the file again out of habit. Still doesn't work. Files are files, directories are directories.

## What I Learned

**Hidden files start with `.`** — this is how Linux stores config files like `.bashrc` and `.ssh` without cluttering your home directory view.

**`ls -a` reveals everything** — the `-a` flag stands for "all." Always use it when you suspect hidden files.

**`find` with no arguments** lists all files recursively from the current directory, hidden or not. More powerful than `ls` for searching.

**The `./` trick from Level 1 applies again** — any filename with special characters at the start needs an explicit path so the shell doesn't misinterpret it.

## Commands Used

| Command | What it did |
|---|---|
| `ls` | Showed nothing — hidden file not displayed |
| `cd inhere` | Navigated into the target directory |
| `find` | Listed all files including hidden ones |
| `cat "./...Hiding-From-You"` | Read the hidden file using explicit path |
