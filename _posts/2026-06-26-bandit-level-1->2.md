---
title: "OverTheWire Bandit: Level 1 → Level 2"
date: 2026-06-26 10:00:00 +0500
categories: [CTF, Bandit]
tags: [linux, cat, special-characters, overthewire, beginner]
---

## The Goal

The password is stored in a file called `-` in the home directory.

## What I Did

Logged in as `bandit1` and ran `ls` to confirm the file was there:

```bash
bandit1@bandit:~$ ls
-
```

One file named `-`. I tried reading it the same way as last level:

```bash
bandit1@bandit:~$ cat -
```

Nothing happened. The terminal just sat there waiting. I pressed `Ctrl+C` to cancel out of it.

I opened the man page for `cat` to figure out why, then searched online for "dashed filename" as the level hinted. That gave me the answer.

The fix:

```bash
bandit1@bandit:~$ cat ./-
```

That printed the password.

## What Was Actually Happening

`-` is a special character in Linux. When you pass `-` to most commands, it means "read from standard input" — your keyboard — instead of a file. So `cat -` wasn't looking for a file at all. It was waiting for me to type something, which is why the terminal froze. `Ctrl+C` sends an interrupt signal that kills the running process.

The fix is `./` — which means "current directory." So `./-` means "the file named `-` inside the current directory." By giving `cat` a path instead of a bare name, the shell treats it unambiguously as a filename and not a flag.

This works for any filename starting with a special character. The same logic applies if a file starts with `--` or any other character the shell might misinterpret.

## What `Ctrl+C` Does

Worth noting since I used it here. `Ctrl+C` sends a SIGINT signal — interrupt — to the currently running process. It's the standard way to cancel a command that's stuck or running longer than you want. You'll use it constantly.

## What I Learned

**`-` means stdin** to most Unix commands. It's a convention, not a rule — commands choose to respect it — but almost all of them do.

**`./` provides an explicit path.** Instead of a bare name the shell might misinterpret, you give it a path that starts with the current directory. Unambiguous.

**`Ctrl+C` kills a running process.** Sends SIGINT. The process can catch and handle it, but most commands just exit.

## Commands Used

| Command | What it did |
|---|---|
| `ls` | Confirmed the file `-` exists |
| `cat -` | Froze — treated `-` as stdin, not a filename |
| `Ctrl+C` | Cancelled the stuck command |
| `man cat` | Checked the manual to understand the `-` behaviour |
| `cat ./-` | Read the file correctly using an explicit path |
