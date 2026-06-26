---
title: "OverTheWire Bandit: Level 7 to Level 8"
date: 2026-06-26 19:00:00 +0500
categories: [CTF, Bandit]
tags: [linux, grep, search, overthewire, beginner]
---

## The Goal

Find the password stored in `data.txt` next to the word `millionth`.

## What I Did

Listed the directory to confirm the file:

```bash
bandit7@bandit:~$ ls
data.txt
```

Tried reading it with `cat`:

```bash
bandit7@bandit:~$ cat data.txt
```

A massive wall of text came out — thousands of lines, each containing a word and what looked like a password. Reading through it manually wasn't an option.

I needed to find the specific line containing `millionth`. That's exactly what `grep` is for:

```bash
bandit7@bandit:~$ grep "millionth" data.txt
millionth	dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
```

One line. The password is right there next to the word.

## What grep Actually Does

`grep` stands for **Global Regular Expression Print**. It reads through a file line by line, checks each line against a pattern you give it, and prints every line that matches.

The basic syntax is:

```bash
grep "pattern" filename
```

It's case sensitive by default. `grep "Millionth"` would find nothing here because the word is lowercase. To make it case insensitive add `-i`:

```bash
grep -i "millionth" data.txt
```

Other useful flags:

| Flag | What it does |
|---|---|
| `-i` | Case insensitive search |
| `-n` | Show line numbers |
| `-v` | Invert — show lines that do NOT match |
| `-r` | Search recursively through directories |
| `-c` | Count matching lines instead of printing them |
| `-l` | Print only filenames that contain a match |

## The Other Commands on This Level

This level lists several commands I didn't need here but are worth understanding:

**`sort`** — sorts lines alphabetically or numerically. Almost always paired with `uniq`.

**`uniq`** — removes consecutive duplicate lines. `sort data.txt | uniq` is the standard combination for finding or removing duplicates.

**`strings`** — extracts readable text from binary files. When `cat` gives you garbage characters, `strings` pulls out only the human-readable parts.

**`base64`** — encodes or decodes base64 data. `base64 -d file` decodes it back to the original content.

**`tr`** — translates or replaces characters. `tr 'A-Z' 'a-z'` converts uppercase to lowercase. `tr -d '\n'` removes all newline characters.

**`tar`** — bundles multiple files into one archive. `tar -xf archive.tar` extracts it.

**`gzip` / `bzip2`** — compression tools. `gzip -d file.gz` and `bzip2 -d file.bz2` decompress files.

**`xxd`** — shows a hex dump of a file. Used when you need to inspect raw bytes — common in reverse engineering and forensics.

**`|` (pipe)** — the most important operator in Linux. Takes the output of one command and feeds it as input to the next:
```bash
cat data.txt | grep "millionth"
```
This does exactly the same thing as `grep "millionth" data.txt` — two ways to get the same result. Pipes become essential when chaining multiple commands together.

## What I Learned

**`grep` is the go-to tool for searching inside files.** When a file is too large to read manually, grep finds exactly what you need instantly.

**The pipe operator chains tools together.** Linux is built around small tools that each do one thing well. The power comes from combining them.

**Reading a massive file with `cat` first** showed me what the structure looked like — word on the left, password on the right — which confirmed that `grep` was the right approach.

## Commands Used

| Command | What it did |
|---|---|
| `ls` | Confirmed `data.txt` exists |
| `cat data.txt` | Showed the file structure — too large to read manually |
| `grep "millionth" data.txt` | Found the exact line containing the password |
