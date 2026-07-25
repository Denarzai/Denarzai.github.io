---
title: "Assessing a Linux Server's Security Baseline — Part 1: Enumeration & Assessment"
date: 2026-07-22 20:45:00 +0500
permalink: /posts/linux-baseline-assessment/
categories: [Hands-On, Linux Security]
tags: [linux, hardening, ssh, firewall, vulnerability-assessment, suid, beginner]
---

A friend of mine is doing a vulnerability-assessment internship and shared one of his practical task sheets with me. Instead of just reading through it, I set up my own lab and worked through it properly — because the only way I actually retain this stuff is by doing it and breaking things myself. This post is the first half: taking a freshly deployed Linux server and assessing its security posture before it ever touches a network. Part 2 will be hardening it and proving the fixes worked.

## Setting Up a Lab I Could Safely Break

The first rule I stuck to: never practise on something you can't afford to destroy. So I built a throwaway environment:

- **VirtualBox** on my Windows machine
- A disposable **Ubuntu Server** VM as the "target"
- **SSH** access into it from my host terminal
- A **clean snapshot** taken *before* touching anything

That snapshot isn't optional. The moment you start changing firewall and SSH settings, one wrong move can lock you out of your own machine. The snapshot is a one-click undo. (I also managed to completely fill my host disk during setup and had to claw back space before I could even take the snapshot — lesson learned: VMs and their snapshots are hungry.)

## The Workflow

The task sheet follows the loop a real assessment uses:

> **Enumerate → Assess → Fix → Verify → Report**

This post covers the first two.

## Step 1 — Enumeration: Just Gather the Facts

Enumeration is simply listing what the system *is* — its identity, accounts, running software, and network exposure. No judging yet, just building an inventory. A small set of commands covers most of it:

```bash
hostname                 # machine name
hostname -I              # IP address
uname -r                 # kernel version
cat /etc/os-release      # OS version
cat /etc/passwd          # user accounts
systemctl --type=service # running services
ss -tuln                 # listening network ports
df -h ; free -h          # disk and memory
```

The single most useful thing I learned to read here was **listening ports**. `ss -tuln` showed that although plenty of services were running, only **one** was actually reachable from outside — SSH on port 22. Everything else (DNS, time sync) was bound to localhost. That one line reframes the whole job: the server's entire external attack surface was a single port.

## Step 2 — Assessment: Now Judge the Facts

This is where the facts get judged. I checked the seven things attackers care about: SSH config, password aging, world-writable files, SUID/SGID binaries, cron jobs, firewall status, and the auth logs.

### The Lesson That Actually Stuck: Tools Produce a Lot of Noise

My first instinct was to flag everything a scan spat out. That instinct is wrong, and two checks taught me why.

**"Hundreds of world-writable files!"** My first scan returned a massive list of world-writable files. Before panicking, I looked at the paths — almost every one was under `/proc` or `/sys`. Those aren't real files on disk; they're virtual interfaces the kernel exposes as files. Re-running the scan while staying on the real filesystem (`find / -xdev …`) returned **nothing**. The scary result was noise.

**"Fourteen privilege-escalation vulnerabilities!"** The SUID scan listed `sudo`, `passwd`, `mount`, `su`, and friends. But those are *meant* to be SUID — `passwd` literally can't update your password otherwise. The weakness is never "a SUID file exists"; it's an *unexpected* SUID file (a SUID copy of bash, or something sitting in `/tmp`). There were none.

Same with the auth log: a handful of failed logins that turned out to be *me* mistyping my own password, not an attacker. **The real skill in assessment isn't running scanners — it's separating genuine findings from expected behaviour.**

### A Thing That Genuinely Confused Me: SSH Passwords vs sudo Passwords

One of my findings was "SSH password authentication is enabled," and my first reaction was: *why would I turn that off — isn't that the password I use for `sudo`?*

It isn't, and untangling this was worth the confusion. There are two completely separate things:

- **`PasswordAuthentication` in SSH** controls whether you can *log in over the network* with a password.
- The **password you type for `sudo`** is local privilege escalation, handled by an entirely different system.

Disabling SSH password authentication does **not** stop `sudo` — you'd still type your password to run privileged commands. What changes is that logging *in* over the network requires an SSH key instead of a guessable password. And that's the point: password login can be brute-forced from anywhere against that exposed port 22; a key can't.

## What I'd Actually Flag (and How Bad Each One Is)

After filtering the noise, the real weaknesses on this default server were:

| Finding | Severity | Why |
|---|---|---|
| SSH password authentication enabled | **Medium** | brute-forceable network login → possible access |
| No active firewall | **Medium** | no network-layer filtering; anything newly exposed is immediately reachable |
| Root SSH login permitted (key-only) | **Low** | key-only already blocks brute force, but a stolen key still gets root — best to disable entirely |
| No password expiration policy | **Low** | a leaked password stays valid indefinitely — a hygiene gap, not directly exploitable |
| No login warning banner | **Low** | no legal / deterrent notice |

A mistake I made and want to flag honestly: I originally rated a couple of these as **High**. That's "severity inflation" — a classic beginner error. If everything is High, nothing stands out. High and Critical are for things that are *directly and severely exploitable* (root login with a password, an unpatched remote code execution, a world-writable `/etc/shadow`). On a default server, most findings are Low–Medium hardening gaps, and the report should reflect that distribution honestly.

## The Takeaway

None of these are exotic exploits. They're **defaults**. A freshly deployed server is not a secure one — which is exactly why baseline hardening exists. In Part 2 I'll fix every one of these — firewall, SSH lockdown, password policy, banner — and then re-run every check to prove the fixes actually took effect. That "prove it worked" step is what separates a checklist from an assessment.
