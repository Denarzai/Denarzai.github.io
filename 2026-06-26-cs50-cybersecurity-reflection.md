---
title: "I Just Finished Harvard's CS50 Cybersecurity — Here's What I Actually Learned"
date: 2026-06-26 16:00:00 +0500
categories: [Learning, CS50 Cybersecurity]
tags: [cs50, cybersecurity, salt-typhoon, final-project, reflection]
---

A few weeks ago I started Harvard's CS50 Introduction to Cybersecurity. I'm a second-year BS Cybersecurity student at FAST NUCES Islamabad, so I wasn't going into it completely blind — but I came out of it with a much clearer picture of how the concepts I'd studied in class actually connect to the real world.

This is a quick reflection on the course and the final project I built for it.

## The Course

CS50 Cybersecurity covers four core areas across five lectures:

- **Lecture 2** — Securing Data: hashing, salting, symmetric and asymmetric encryption, digital signatures, end-to-end encryption
- **Lecture 3** — Securing Systems: HTTPS, TLS, firewalls, VPNs, SSH, malware
- **Lecture 4** — Securing Software: SQL injection, XSS, CSRF, buffer overflow
- **Lecture 5** — Preserving Privacy: cookies, browser fingerprinting, DNS, Tor

I've written individual posts on each of these topics if you want to go deeper — they're in the [CS50 Cybersecurity](/categories/cs50-cybersecurity/) category on this blog.

What I appreciated about the course is that it doesn't stay abstract. Every concept gets grounded in something real. You don't just learn what a man-in-the-middle attack is — you understand why HTTPS exists, how TLS prevents it, and what happens when a Certificate Authority gets compromised.

## The Final Project — Salt Typhoon

For the final project, you pick a real-world cybersecurity incident from the past 24 months, research it, and present a 7-10 minute video explanation connecting it to the course material.

I chose **Salt Typhoon** — a Chinese state-sponsored attack on US telecommunications infrastructure, publicly disclosed in September 2024. I'd never heard of it before. The name came up while I was looking for a topic that connected to multiple lectures, and once I started reading I couldn't have chosen anything else. It ended up connecting to all four lectures simultaneously.

Before this project I had no idea what CALEA was. I didn't know there was a 1994 US law requiring every phone company to build a wiretapping backdoor into their network. I didn't know that "lawful intercept" systems were a specific piece of software sitting inside telecom infrastructure — something you could actually reach if you got deep enough into the network.

Salt Typhoon reached it.

The attackers exploited CVE-2023-20198 — a CVSS 10.0 vulnerability in Cisco IOS XE's web management interface that allowed unauthenticated users to create admin accounts — to get a foothold in the core routing infrastructure of nine major US telecoms including AT&T, Verizon, and T-Mobile. From there they moved laterally until they reached the CALEA lawful intercept systems. They accessed call records for over a million people and in some cases obtained actual audio recordings of calls made by Donald Trump, JD Vance, and Kamala Harris's campaign staff.

No encryption was broken. No exotic zero-day was used after the initial access. They walked through a door that US law required to exist.

Researching this taught me more about how real attacks actually work than anything I'd studied in a classroom. The attack chain — reconnaissance, initial access via a known CVE, privilege escalation, persistence through GRE tunnels, lateral movement — maps directly to frameworks like MITRE ATT&CK that the industry actually uses. Reading about it made those frameworks feel real in a way they didn't before.

The core lesson I kept coming back to: **you cannot build a backdoor that only opens for authorised people.** CALEA was designed for law enforcement. China's Ministry of State Security used it instead. The door is either secure or it isn't.

## The Presentation

If you want to watch the full 7-minute presentation — covering the attack timeline, the technical exploit chain, the CALEA connection, course connections, and recommendations — it's on YouTube:

{% include embed/youtube.html id='4nZZHqxrjhs' %}

## What's Next

The course is done but the learning isn't. I'm currently working through OverTheWire Bandit (writeups are on this blog) and about to start TryHackMe's Jr Penetration Tester path. The goal is to move from understanding concepts to being able to apply them hands-on.

If you're a cybersecurity student looking for a course that actually explains the *why* behind the concepts rather than just the *what*, CS50 Cybersecurity is worth your time. It's free, it's well taught, and the final project forces you to go find something real and understand it deeply enough to explain it to someone else.

That last part turned out to be the most valuable thing about it.
