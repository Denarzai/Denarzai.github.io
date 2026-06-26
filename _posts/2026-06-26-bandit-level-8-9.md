---
title: "OverTheWire Bandit: Level 8 to Level 9"
date: 2026-06-26 19:20:00 +0500
categories: [CTF, Bandit]
tags: [linux, sort, uniq, piping, overthewire, beginner]
---

## The Goal

Find the one line in `data.txt` that appears only once. Every other line is a duplicate.

## What I Did

Listed the file and opened it with `cat`. Hundreds of lines of what looked like random passwords, most of them repeated multiple times. Reading through it manually wasn't an option.

The level page pointed to a piping and redirection tutorial. I read through it, understood how pipes work, then figured out the solution from there.

The key insight: I needed two commands chained together.

```bash
bandit8@bandit:~$ sort data.txt | uniq -u
EjmOSvuAu7sGAHqHVcBDPirRe9T03kxl
```

One line. That's the password.

## What Was Actually Happening

**`uniq` only removes consecutive duplicate lines** — it checks each line against the one directly above it. If duplicates are scattered throughout the file in different positions, `uniq` won't catch them.

**`sort` fixes this** by rearranging all lines alphabetically first. After sorting, every duplicate ends up next to its copies. Now `uniq` can see them.

**`uniq -u`** prints only the lines that are unique — lines that appear exactly once. Without `-u`, `uniq` collapses duplicates into one but still prints them. With `-u` it filters them out entirely, leaving only what appeared once in the whole file.

The pipe `|` connects the two — it takes the sorted output from `sort` and feeds it directly into `uniq -u` without writing anything to disk.

## Why sort + uniq is a Classic Combination

These two commands almost always appear together. `sort` alone just reorders lines. `uniq` alone only works on adjacent lines. Together they solve a whole class of problems:

```bash
sort file.txt | uniq          # remove all duplicates
sort file.txt | uniq -u       # show only unique lines
sort file.txt | uniq -d       # show only duplicate lines
sort file.txt | uniq -c       # count how many times each line appears
```

The `-c` flag is particularly useful — it shows you exactly how many times each line appears, sorted output looking like:

```
   3 duplicated_line
   1 unique_line
   5 another_duplicate
```

## What I Learned

**`uniq` only compares adjacent lines** — always sort first unless the file is already sorted.

**`uniq -u` filters for unique lines** — the opposite of `-d` which shows only duplicates.

**Pipes connect commands without temporary files** — `sort data.txt | uniq -u` processes everything in memory. No intermediate file needed.

**Reading the manual before solving pays off** — understanding piping from the tutorial made it obvious which tools to combine and why.

## Commands Used

| Command | What it did |
|---|---|
| `cat data.txt` | Showed the file — hundreds of repeated lines |
| `sort data.txt` | Sorted all lines alphabetically, grouping duplicates together |
| `sort data.txt \| uniq` | Removed consecutive duplicates after sorting |
| `sort data.txt \| uniq -u` | Printed only the line that appeared exactly once |
