---
title: "Five Projects, One Server: What I Learned Securing Linux From Scratch"
date: 2026-07-29 04:47:00 +0500
permalink: /posts/linux-security-lab-capstone/
categories: [Hands-On, Linux Security]
tags: [linux, security, hardening, blue-team, vulnerability-assessment, reflection]
---

A friend of mine is doing a vulnerability-assessment internship and shared his practical task sheet with me — five projects covering the core skills of a junior VA analyst. I decided to work through *all* of them on my own home lab, because the only way I actually learn security is by doing it and breaking things. This post ties the whole thing together: five projects, one server, and the lessons that outlasted any single command.

I started this having **never set up a virtual machine.** By the end I had a hardened, access-controlled, trimmed, and monitored Linux server — plus a full baseline inventory. Here's the arc.

## The five projects

**[Project 1 — Baseline Assessment & Hardening](/posts/linux-baseline-assessment/)** ([and the hardening half](/posts/linux-baseline-hardening/))
I took a freshly deployed server and ran the analyst's loop: *enumerate → assess → fix → verify*. The big realisation was that a *default* server is not a *secure* one — the weaknesses were things like password-based SSH and no firewall, all defaults. I hardened SSH to key-only, locked down the firewall, and — the part that matters — *proved* each fix worked instead of assuming it.

**[Project 2 — Users, Groups & Permissions](/posts/linux-access-control-rbac/)**
Building departmental access control (HR / Finance / IT) taught me how Linux permissions *actually* work. The thing that finally clicked: **a group protects nothing on its own.** Access control needs three separate pieces — the *who* (groups), the *what* (directories), and the *rules* (owner/group/other bits, plus ACLs for exceptions). Groups are keycards; you still need rooms with locks.

**[Project 3 — Process, Service & Cron Review](/posts/linux-attack-surface-reduction/)**
Every running service is code an attacker could exploit, so I reviewed everything and trimmed what a headless VM doesn't need. I learned the real difference between *running* and *enabled*, between `disable` and `mask`, and — most usefully — what a **normal, boring process list looks like**, so an abnormal one would stand out.

**[Project 4 — Log Analysis & Monitoring](/posts/linux-log-analysis-incident/)**
This flipped me to the blue-team side: mine the logs, reconstruct an incident, write it up. I simulated an SSH brute-force and hunted it in `auth.log`, learning to tell a real attack from admin noise by **source, pattern, and rate**, and to prove whether it succeeded from the evidence.

**Project 5 — System Inventory & Documentation**
The finale: assemble everything into one clean baseline document — hardware, software, network, security — the reference sheet a VA engagement starts from. Fittingly, the inventory *showed* my earlier work: fewer services, one exposed port, key-only SSH. The hardening had held.

## The lessons that repeated every single time

Individual commands fade. These didn't:

**1. Verify — don't assume.** Every project ended the same way: not "I changed it" but "I *proved* it changed." The strongest proof came from the *attacker's* side — trying a password login and being *rejected* beats reading a config value. A config file will happily lie to you (mine did — a `cloud-init` drop-in silently kept password login on until I checked the *effective* config).

**2. You can't spot abnormal until you know normal.** The process baseline in Project 3 was the foundation for the log analysis in Project 4. Security is, over and over, *noticing what breaks the baseline* — which means you have to know the baseline cold.

**3. Separate real findings from tool noise.** A scan that returns "hundreds of world-writable files" or "14 SUID vulnerabilities" is mostly noise (`/proc` isn't real files; `sudo` is *supposed* to be SUID). Learning to filter expected behaviour from genuine risk is the skill that separates an analyst from someone who just runs scanners.

**4. The unglamorous stuff is where competence lives.** Take a snapshot before you change anything. Allow SSH through the firewall *before* you enable it. Mask the socket, not just the service. None of it is exciting; all of it is the difference between "done" and "locked myself out."

## The struggles taught the most

I won't pretend it was smooth. I hit a WSL networking wall (turns out WSL has its own `127.0.0.1`, separate from Windows). I completely filled my host disk mid-setup. I chased a config override that was silently undoing my SSH hardening. My VM's clock drifted 13 hours and scrambled my log timestamps — which taught me that an incident timeline is only as trustworthy as the system clock. Every one of those was annoying in the moment and, in hindsight, the most memorable lesson of its project.

## Where this leaves me

Five reports, one hardened box, and — more importantly — a workflow I can *repeat and explain*, not just copy-paste. Enumerate, assess, fix, verify, document. Understand who/what/rules. Know your baseline. Prove everything.

I did this to build real, defensible skills on the way toward offensive security — and the biggest surprise was how much *defending* a system teaches you about *attacking* one. You can't harden what you don't understand, and you can't break what you can't map. Same knowledge, both directions.

On to the next thing.
