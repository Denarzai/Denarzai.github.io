---
title: "OverTheWire Bandit: Level 2 → Level 3"
date: 2026-06-26 12:00:00 +0500
categories: [CTF, Bandit]
tags: [linux, cat, spaces, filenames, overthewire, beginner]
---

## The Goal

The password is stored in a file called `spaces in this filename` in the home directory.

## What I Did

Logged in as `bandit2` and confirmed the file:

```bash
bandit2@bandit:~$ ls
spaces in this filename
```

Tried reading it directly:

```bash
bandit2@bandit:~$ cat spaces in this filename
```

That failed — the shell treated each word as a separate argument, looking for four different files called `spaces`, `in`, `this`, and `filename`. None of them exist.

Checked the helpful reading material on the level page, which confirmed the fix — wrap the filename in quotes:

```bash
bandit2@bandit:~$ cat "spaces in this filename"
```

That printed the password.

## What Was Actually Happening

The shell splits commands into arguments using spaces as separators. So `cat spaces in this filename` looks like you're passing four separate filenames to `cat`. Wrapping the whole thing in quotes tells the shell to treat everything inside as a single argument.

Single quotes work too:

```bash
cat 'spaces in this filename'
```

So does escaping each space with a backslash:

```bash
cat spaces\ in\ this\ filename
```

All three are valid. Quotes are the most readable.

## What I Learned

**Spaces in filenames cause problems** because the shell uses spaces to separate arguments. This comes up constantly — not just in CTFs but in real scripting work. A script that handles filenames without quoting will break the moment someone names a file with a space in it.

**Quoting is the standard fix.** Double quotes allow variable expansion inside them. Single quotes treat everything literally. For a plain filename with spaces either works the same way.

**Tab completion handles this automatically.** If you type `cat sp` and press Tab, the shell autocompletes to `cat spaces\ in\ this\ filename` with the spaces escaped. Useful to know for later.

## Commands Used

| Command | What it did |
|---|---|
| `ls` | Confirmed the filename with spaces |
| `cat spaces in this filename` | Failed — shell split it into four arguments |
| `cat "spaces in this filename"` | Worked — quotes kept it as one argument |
