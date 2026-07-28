---
title: "Reading the Logs: Investigating an SSH Brute-Force on Linux"
date: 2026-07-28 22:26:00 +0500
permalink: /posts/linux-log-analysis-incident/
categories: [Hands-On, Linux Security]
tags: [linux, log-analysis, incident-response, ssh, auth-log, journalctl, beginner]
---

The scenario for this one flipped me from hardening to **investigating**: a server has seen repeated authentication failures — mine the logs, build a timeline, and write an incident summary. This is the blue-team side of security, the work a SOC analyst does. Same lab VM, but now I'm the one reading the story the logs tell.

## Where Linux keeps its logs

Modern Ubuntu runs two logging systems side by side:

- **Text log files** in `/var/log/` — most importantly `auth.log`, which records SSH logins, `sudo`, `su`, and sessions.
- **The systemd journal** — a structured binary log you read with `journalctl` (filterable by service with `-u`, by boot with `-b`, by time with `--since`).

I went looking for login history with the classic `last` and `lastb`… and `last` **wasn't installed**. Turns out Ubuntu 26.04 replaced the old `wtmp`/`btmp` accounting with a new SQLite-based system called **`wtmpdb`**. Even after installing it, `lastb` didn't exist and `/var/log/btmp` was empty — meaning **failed logins on this box are only recorded in `auth.log`**. First real lesson: know where your data actually is before you start hunting. On this machine, `auth.log` is the whole story.

## The lesson hiding in the timestamps

While reading `syslog` I noticed `chronyd` complaining: *"System clock wrong by 48020 seconds"* and *"Can't synchronise: no selectable sources."* The VM's clock had drifted about **13 hours** off. That explained days of confusing timestamps — and it's a genuine analyst lesson: **an incident timeline is only as trustworthy as the system clock.** If the time is wrong, your whole reconstruction is wrong. Verifying time sync is step zero of log analysis, not an afterthought.

## Simulating the incident, then hunting it

The logs were quiet, so I generated something to find: from my host I fired a handful of SSH attempts as fake usernames — a mini brute-force scan:

```bash
ssh -p 2222 admin@<target>
ssh -p 2222 root@<target>
ssh -p 2222 test@<target>
ssh -p 2222 oracle@<target>
ssh -p 2222 hacker@<target>
```

Then I hunted them:

```bash
sudo grep -Ei "failed|invalid user|authentication failure" /var/log/auth.log
```

This is where a habit clicked into place: **`grep`, not `cat`.** `tail` only shows the last few lines and `cat` dumps thousands where failures are buried in normal noise. `grep` filters the entire file down to exactly the events you want. Browsing is `cat`; *hunting* is `grep`.

There it was — five attempts from one source in about two seconds:

```
04:11:51  Invalid user admin from 10.0.2.2
04:11:51  Connection reset by authenticating user root 10.0.2.2
04:11:51  Invalid user test from 10.0.2.2
04:11:52  Invalid user oracle from 10.0.2.2
04:11:53  Invalid user hacker from 10.0.2.2
```

## A detail worth staring at

Notice `admin`, `test`, `oracle`, `hacker` all say **`Invalid user`** — but `root` says *"authenticating user root."* The difference is that **`root` actually exists**, so SSH words it differently. That's not trivia: it means **failed logins leak information.** An attacker enumerating usernames learns which accounts are real *just from the error messages*, then focuses on those. Even a completely failed attempt hands the attacker intel.

## The investigation

With the events in hand, the analyst's job is judgment — is this an attack, and does it matter?

- **Suspicious or benign?** The burst is clearly suspicious: five different usernames, one source, two seconds. That **rate** is the fingerprint — no human types that fast, so it's automated tooling. Contrast that with the other failures in the log: my own occasional mistyped `sudo` password, from a local session, one account. Same "authentication failure" text, completely different meaning. Source, pattern, and rate are what separate them.
- **Did it succeed?** No — and I proved it from the *absence* of success. There's no `Accepted` event tied to that activity, and my failed `su` attempt shows `FAILED SU`. The only successful logins in the whole file are my own key-based sessions.
- **Why was it doomed?** Because of the hardening from an earlier project: SSH is **key-only**. Brute-force works by guessing passwords — with no password accepted at all, there's nothing to guess. Dead on arrival.

## The part that "it failed" hides

Here's the thing a beginner (me, a week ago) would stop at: *it failed, we're fine.* But it only failed because one control — key-only auth — held. **Nothing on the server detected or blocked the attempt.** It could have hammered the port indefinitely and no one would have known. Real defense is layered: `fail2ban` to auto-ban repeat offenders, restricting SSH to the admin's own IP so scanners can't even reach it, and **alerting** on failed-login spikes so a human finds out. Prevention *and* detection.

## Takeaway

Log analysis turned out to be less about commands and more about **reconstructing a story from scattered events** — a failed login here, a source IP there, a timestamp that ties them together. And it leaned entirely on the previous project's lesson: you can only recognise an *abnormal* pattern because you've stared at *normal* long enough to know the difference. Four projects in, that theme keeps proving itself — normal is the baseline; security is noticing what breaks it.
