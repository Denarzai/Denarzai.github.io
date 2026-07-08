---
title: "TryHackMe Pre-Security: Module 7 — Attacks and Defenses"
date: 2026-07-08 10:02:00 +0500
categories: [Learning, TryHackMe, Pre-Security]
tags: [tryhackme, cia-triad, cryptography, gobuster, hydra, ethical-hacking, offensive-security, defensive-security, beginner]
---

## Overview

Module 7 was the final module of TryHackMe's Pre-Security path and the most directly security-focused. Four rooms covered the CIA Triad, cryptography basics, a hands-on ethical hacking exercise, and an introduction to defensive security — ending with my first complete attack chain on a web application using real tools and a look at how defenders protect against exactly those attacks.

## Room 1 — The CIA Triad

The three pillars that define what security actually means:

- **Confidentiality** — information is only accessible to authorised individuals. Encryption, access controls, and authentication all serve this.
- **Integrity** — information cannot be modified without authorisation. Hashing, digital signatures, and audit logs serve this.
- **Availability** — systems and data are accessible when needed. Redundancy, backups, and DDoS protection serve this.

Every security control you'll ever encounter maps to one or more of these. A firewall protects availability and confidentiality. Encryption protects confidentiality and integrity. Knowing which pillar a control serves helps you reason about what gaps might exist and what an attacker is targeting.

## Room 2 — Cryptography Basics

Symmetric and asymmetric encryption, how they combine in practice, and where cryptography fits in the security picture.

This reinforced what I covered in CS50 Cybersecurity and in the Bandit levels involving TLS. The key insight this room articulated clearly: **asymmetric encryption solves the key distribution problem** — you can share your public key openly, and anyone can use it to encrypt data that only your private key can decrypt. But asymmetric operations are computationally expensive, so real systems use asymmetric encryption just to establish a shared symmetric key, then switch to symmetric for the actual data transfer. That's exactly what happens in a TLS handshake.

The room also made the point that cryptography is one layer in a larger security picture. Strong encryption doesn't help if passwords are weak, keys are stored insecurely, or users are socially engineered. This is the systems thinking that separates security professionals from people who just know individual tools.

## Room 3 — Become a Hacker

The most hands-on room of the entire Pre-Security path. A complete ethical hacking exercise against a simulated web application.

### The Scenario

Assessing Mike's online shop before launch — find any exposed pages that shouldn't be publicly accessible, then demonstrate the risk by exploiting them.

### Step 1 — Manual Enumeration

Manually tested common paths by adding them to the URL — `/sitemap`, `/mail`, `/register`, `/login`, `/admin`. Only `/login` returned a valid page. A hidden login page. On its own not critical, but the first domino.

### Step 2 — Automated Directory Enumeration with Gobuster

```bash
gobuster dir --url http://www.onlineshop.thm/ -w /usr/share/wordlists/dirbuster/directory-list.txt
```

Gobuster confirmed `/login` and returned its status code. This is the same tool introduced in TryHackMe Module 1 — `dirb` does the same job, gobuster just does it faster using concurrent requests.

### Step 3 — Credential Attack with Hydra

With a login page found, the next step was testing whether weak credentials would grant access. Hydra automates dictionary attacks — trying each password in a wordlist against the login form:

```bash
hydra -l admin -P passlist.txt www.onlineshop.thm http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect" -V
```

Breaking down the command:
- `-l admin` — known username to test against
- `-P passlist.txt` — wordlist of passwords to try
- `http-post-form` — specifies an HTTP POST form attack
- `"/login:username=^USER^&password=^PASS^:F=incorrect"` — the form structure and what a failed login looks like so Hydra knows when to stop
- `-V` — verbose output showing each attempt

The password `qwerty` was found. Logged in and retrieved the flag: `THM{born_to_hack!}`.

### The Domino Effect

The attack chain: hidden page discovery → credential attack → authenticated access → sensitive data retrieved. No single step was catastrophic alone — a hidden login page and weak passwords are both minor issues individually. Combined they gave full admin access. This is how real breaches work.

The mindset shift the room described is worth keeping: don't ask whether something works as intended, ask how it could be misused.

## Room 4 — Become a Defender

The final room flipped perspectives — instead of attacking, defending. The blue team's job is to protect client infrastructure by maintaining **visibility** across systems, **preventing** threats where possible, **detecting** what gets through, and **mitigating** damage once something is identified.

Key defensive roles introduced:

**SOC (Security Operations Centre)** — monitors networks and systems around the clock, investigating alerts and suspicious activity. This is what I covered in TryHackMe Module 1 — the SOC analyst blocking the malicious IP at the firewall.

**Threat Intelligence** — researches current attackers, techniques, and trends so the organisation can prepare before attacks happen rather than reacting after.

**DFIR (Digital Forensics and Incident Response)** — investigates after a breach has occurred. How did the attacker get in? What did they access? How do you contain and recover?

What this room made clear: defenders need to understand offensive techniques as well as defensive ones. You can't protect against an attack you don't understand. The same knowledge that makes an ethical hacker effective makes a defender more capable.

## Completing the Pre-Security Path

This room marks the end of TryHackMe's Pre-Security path. Looking back across all seven modules:

Computer fundamentals → Operating systems → Networking → Web → Security concepts — each layer built on the previous one. The value wasn't any single concept but seeing how they connect into a complete picture. The boot process is an attack surface. Networking protocols have no built-in authentication. Web applications expose databases through user input. Cryptography is one layer, not a complete solution.

The Jr Penetration Tester path is next — that's where the actual offensive techniques begin in depth, starting from this foundation.

---
*TryHackMe username: sdenarzai786*
