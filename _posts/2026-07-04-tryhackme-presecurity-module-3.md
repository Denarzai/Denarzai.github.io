---
title: "TryHackMe Pre-Security: Module 3 — Operating Systems"
date: 2026-07-04 04:47:00 +0500
categories: [Learning, TryHackMe, Pre-Security]
tags: [tryhackme, linux, windows, operating-systems, privilege-escalation, cli, beginner]
---

## Overview

Module 3 of TryHackMe's Pre-Security path covered operating systems across five rooms — OS fundamentals, Windows basics, Linux CLI, Windows CLI, and a practical privilege escalation exercise. Most of the individual concepts weren't new to me at this point, but working through them in a structured environment filled in gaps and showed me how these pieces connect in real scenarios.

## Room 1 — Operating Systems Introduction

The first room covered what an OS actually does — managing hardware, processes, memory, and user permissions. The key distinction was between **kernel space** and **user space**:

- **Kernel space** — the privileged core of the OS with direct hardware access. The kernel manages everything below the surface.
- **User space** — where applications run with limited permissions. If an application crashes here it doesn't bring down the whole system.

This distinction matters in security because most privilege escalation attacks are attempts to move from user space into kernel space — or to gain the privileges of a process running closer to the kernel than you should have.

## Room 2 — Windows Basics

Windows interface fundamentals — Task Manager, Windows Security, Defender Firewall, Windows Update. Most of this was familiar from daily use but seeing it framed through a security lens was useful. Task Manager isn't just for killing frozen apps — it's a real-time view of what's running on a system, which is one of the first places you look during incident response to identify suspicious processes.

Windows Defender Firewall controls what network traffic is allowed in and out. Understanding what it does conceptually is important before getting into firewall rule analysis in later modules.

## Room 3 — Linux CLI Basics

Navigating the Linux filesystem, searching for files, reading configuration files, inspecting system information. Most of this I already knew from working through OverTheWire Bandit — `ls`, `cat`, `find`, `grep` and the logic of Linux file permissions were all covered there in much more depth and with actual problem solving rather than guided exercises.

What Bandit teaches through challenges, this room taught through explanation. Having done Bandit first meant I already had the practical intuition and this room just gave me the formal framing around concepts I'd already applied.

## Room 4 — Windows CLI Basics

The Windows Command Prompt equivalent of the Linux CLI room — navigating files, locating hidden files, reading file contents, gathering system and network information from the terminal. The key takeaway applies to both operating systems: the command line gives you speed, precision, and the ability to automate tasks that would take many clicks in a GUI.

In real-world security work, you often can't rely on a GUI — remote access, restricted environments, and incident response scenarios all require CLI comfort on both Windows and Linux.

## Room 5 — Privilege Escalation Practical

The most hands-on room of the module. A complete attack chain on a Linux system:

1. SSH into the system as `sammie`
2. Lateral movement to `johnny`
3. Escalation to `root`
4. Retrieve the flag from `/root/flag.txt`

A few things worth noting from working through this:

**Typos cost time.** Multiple failed `su` attempts because of `jhonny` and `jhony` instead of `johnny`. Running `whoami` and `id` first to confirm your current user context before attempting account switches prevents this — something I'll carry forward.

**Credentials get exposed in unexpected places.** Passwords discovered through shell history and sticky notes. In a real assessment this is one of the first places to look after gaining initial access.

**Privilege escalation is a chain, not a single step.** Moving from sammie → johnny → root required understanding each stage — what credentials were available, which user had access to what, and how to move progressively through the access chain.

## The Bigger Picture

What this module solidified wasn't individual commands — it was seeing how these pieces connect. Kernel space versus user space explains why privilege escalation is possible and what it means. The Linux file permission system I learned in Bandit is what makes setuid binaries and misconfigured files exploitable. The Windows security tools are the defensive layer that attackers try to bypass.

The concepts are no longer isolated facts. They're parts of a system I can now see working together.

## What's Next

Module 4 covers networking fundamentals — how data actually moves across networks at the protocol level. This is where the conceptual groundwork from the first three modules starts becoming directly applicable to understanding attacks.

---
*TryHackMe username: sdenarzai786*
