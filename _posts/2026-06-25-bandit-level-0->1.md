---
title: "OverTheWire Bandit: Level 0 → Level 1"
date: 2026-06-25 12:00:00 +0500
categories: [CTF, Bandit]
tags: [linux, ssh, cat, ls, overthewire, beginner]
---

## The Goal

Find the password for Level 1. It's stored in a file called `readme` in the home directory.

## What I Did

After logging in as `bandit0`, the first thing I did was run `ls` to see what was in the home directory:

```bash
bandit0@bandit:~$ ls
readme
```

One file. `readme`. My first instinct was to `cd` into it:

```bash
bandit0@bandit:~$ cd readme
-bash: cd: readme: Not a directory
```

That failed, which made sense after a second of thinking — `cd` is for directories, not files. `readme` is a file. I needed to read it, not navigate into it.

I wasn't sure which command to use, so I checked the man page for `cat`:

```bash
man cat
```

`cat` outputs the contents of a file to the terminal. That's what I needed. I also explored the `/home` directory out of curiosity — ran `cd ..` and `ls` to see what other users existed on the system. There were a lot of them — bandit0 through bandit33 plus dozens of other wargame users. I didn't fully understand what I was looking at but made a mental note for later.

Back in the home directory, I ran:

```bash
bandit0@bandit:~$ cat readme
```

That printed the password.

I also tried `cat -A` on my local passwords file while setting things up. The `-A` flag shows hidden characters — every line ends with a `$` to mark the line ending, and you can spot invisible spaces or Windows-style line endings that wouldn't show up otherwise. Not needed here but useful to know.

## What I Learned

**`cat`** reads and prints the contents of a file. The name stands for concatenate — it was originally designed to join files together, but reading a single file is its most common use.

**`cd` only works on directories.** If you try to `cd` into a file you get an error. Files and directories are different things in Linux — `ls -l` shows which is which (directories have a `d` at the start of the permissions column).

**The `/home` directory** contains a folder for every user on the system. On this server that means every Bandit level has its own user account, and each user can only read their own files by default. This is Linux file permissions in action — something the later levels get heavily into.

## The Connection to What I've Been Studying

This level is simple on the surface but it demonstrates something from CS50 Cybersecurity: **least privilege**. Each bandit user only has access to their own home directory. Even though I could see all the other user folders listed in `/home`, I couldn't read their files. Access is controlled at the user level, and every user gets exactly the permissions they need — nothing more.

## Commands Used

| Command | What it did |
|---|---|
| `ls` | Listed files in the current directory |
| `cd readme` | Failed — `readme` is a file, not a directory |
| `cd ..` | Moved up to the parent directory `/home` |
| `man cat` | Read the manual for `cat` |
| `cat readme` | Printed the contents of the file |
| `cat -A` | Shows file contents with hidden characters visible |
