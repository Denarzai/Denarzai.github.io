---
title: "OverTheWire Bandit: Level 0"
date: 2026-06-25 03:00:00 +0500
categories: [CTF, Bandit]
tags: [linux, ssh, overthewire, beginner]
---

## What the Level Wants

The goal is to log into the game via SSH using:
- Host: `bandit.labs.overthewire.org`
- Port: `2220`
- Username: `bandit0`
- Password: `bandit0`

## What I Learned

SSH is how you securely connect to a remote machine over a network.
The `-p` flag lets you specify a non-default port.

## The Command

```bash
ssh -p 2220 bandit0@bandit.labs.overthewire.org
```

## What Happened

The server accepted the connection and displayed a welcome banner.
From here the goal is to find the password for Level 1.

## Key Takeaway

`man ssh` and searching `/SYNOPSIS` shows the full command structure.
Flags modify command behaviour — `-p` overrides the default port 22.
