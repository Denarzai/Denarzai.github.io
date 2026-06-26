---
title: "TryHackMe Pre-Security: Module 1 — Introduction to Cyber Security"
date: 2026-06-26 20:00:00 +0500
categories: [Learning, TryHackMe]
tags: [tryhackme, offensive-security, defensive-security, soc, careers, beginner]
---

## Overview

First module of TryHackMe's Pre-Security path done. Four rooms, all introductory — most of the concepts weren't new to me after completing CS50 Cybersecurity, but this was the first time I actually got to see some of them in practice rather than just reading about them.

## Offensive Security Intro

The offensive security room was the most hands-on part of the module. The core concept is simple: ethical hackers attack systems with permission to find vulnerabilities before malicious actors do.

The practical part involved using a tool called `dirb` to find hidden pages on a website. The idea is straightforward: websites have pages that aren't linked anywhere publicly. An attacker can discover them by brute-forcing common directory and filename patterns against the server.

The command looks like this:

```bash
dirb http://target.com /usr/share/dirb/wordlists/common.txt
```

It tries hundreds or thousands of common paths from a wordlist — `/admin`, `/login`, `/backup`, `/config` — and reports back which ones return a valid response from the server. A 200 means the page exists. A 404 means it doesn't. This is called **directory enumeration** and it's one of the first steps in any web application penetration test.

**`dirb` vs `gobuster`**

`dirb` is the older tool — simpler, pre-installed on most security distros, sends requests one at a time. `gobuster` is newer, written in Go, and significantly faster because it runs multiple requests concurrently. Both do the same job. Most penetration testers use gobuster today purely because of speed, but dirb is still widely used for quick tests and learning.

The wordlist is what actually matters in both cases. A wordlist is a text file containing thousands of common directory and file names. The most widely used collection is **SecLists** — a community-maintained repository covering directories, passwords, usernames, and more.

What made this click for me: the server has no way to distinguish enumeration from normal traffic. It's just HTTP requests. The defence isn't hiding the pages — it's making sure those pages are properly authenticated and don't expose anything sensitive even if discovered.

## Defensive Security Intro

The defensive security room covered the other side — how organisations detect and respond to attacks.

The main concept introduced was the **SOC** — Security Operations Centre. A SOC is a team whose job is to monitor an organisation's systems around the clock, looking for signs of malicious activity. The tools they use collect logs from across the network — firewalls, servers, endpoints — and surface anything suspicious.

The practical scenario involved looking at logs, identifying a suspicious IP address making unusual requests, and blocking it at the firewall level. That loop — detect, investigate, respond — is the core of defensive security work.

This connected directly to what I covered in my CS50 final project. Salt Typhoon stayed undetected inside US telecom networks for potentially two years. That's not because the attack was undetectable — it's because nobody was looking closely enough. A well-run SOC with proper monitoring might have caught the unusual GRE tunnel traffic much earlier.

## Careers in Cybersecurity

The third room mapped out the different career paths in the field. A few that stood out:

**Penetration Tester** — hired to attack systems legally. Requires deep technical knowledge of how systems can be broken. Output is a report documenting every vulnerability found and how to fix it.

**SOC Analyst** — monitors for threats in real time. More process-driven than offensive roles. Entry point for many people into the industry.

**Malware Analyst / Reverse Engineer** — analyses malicious software to understand how it works. Heavy use of assembly language and debugging tools. The most technically demanding path in my opinion.

**Security Engineer** — builds and maintains the security infrastructure — firewalls, SIEM systems, detection rules. Sits between development and security.

**Incident Responder** — called in when a breach has already happened. Investigates what occurred, contains the damage, and helps the organisation recover.

## The Career Questionnaire

The final room was a short scenario-based questionnaire. You're given situations — a suspicious file appears on a server, an alert fires at 3am, you find a vulnerability in a client's system — and you choose how you'd respond.

My results pointed toward **Penetration Testing**, which matched what I already suspected. The scenarios I found most interesting were the ones involving actively probing systems, finding weaknesses, and figuring out how something could be broken. The questionnaire isn't a definitive answer but it's a useful way to think about which situations you find interesting versus which ones feel like work.

Penetration testing aligns well with my background — assembly language, C++ systems programming, and understanding how software works at a low level are all directly relevant to finding and exploiting vulnerabilities.

## What I Already Knew vs What Was New

Most of the concepts — firewalls, encryption, what a SOC does — I'd covered in CS50. What was new was actually running a tool and seeing the output. Watching `gobuster` enumerate directories and return results made the concept concrete in a way that reading about it doesn't.

That's the value of TryHackMe over a lecture-based course. The theory makes more sense once you've done the thing, even once, in a controlled environment.

## What's Next

Module 2 covers networking fundamentals — how the internet actually works at the protocol level. This is going to be more technical and more useful for understanding why attacks work the way they do.

---
*TryHackMe username: sdenarzai786*
