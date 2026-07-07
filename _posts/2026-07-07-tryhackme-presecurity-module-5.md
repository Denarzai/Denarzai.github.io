---
title: "TryHackMe Pre-Security: Module 5 — Networking Fundamentals"
date: 2026-07-07 19:47:00 +0500
categories: [Learning, TryHackMe]
tags: [tryhackme, networking, osi-model, tcp-ip, subnetting, dhcp, arp, firewalls, vpn, beginner]
---

## Overview

Module 5 of TryHackMe's Pre-Security path covered networking fundamentals across five rooms — the internet and device identification, LAN topologies and subnetting, the OSI model, packets and protocols, and network extension with firewalls and VPNs. Most of this overlapped heavily with my university networking coursework using Cisco Packet Tracer. The value here was revision with a different perspective, and in a few cases filling in gaps I didn't know I had.

## Room 1 — What is Networking?

Internet fundamentals, device identification via IP and MAC addresses, and ICMP ping. All of this was already solid from networking lab work — OSPF, EIGRP, RIPv2 configurations in Packet Tracer gave me a practical understanding of how networks function before this course ever defined them. The revision here just reinforced what I already knew.

## Room 2 — Intro to LAN

LAN topologies, subnetting, ARP, and DHCP. Again familiar territory — VLSM subnetting and the ARP/DHCP processes are things I've configured and troubleshot directly. What TryHackMe does differently is frame these concepts from a security angle: ARP spoofing exists because ARP has no authentication, DHCP starvation attacks exist because DHCP has no per-client limits by default. Seeing the security implications of protocols I already knew added a useful layer.

## Room 3 — The OSI Model

All seven layers covered with a practical game at the end. I knew the OSI model from university but was only firmly confident on layers 1-4 (Physical, Data Link, Network, Transport). The Presentation layer (layer 6) and Session layer (layer 5) were genuinely new to me — I'd memorised the names but never understood what they actually do.

**Session layer** manages and maintains connections between applications — it establishes, controls, and terminates sessions. Think of a login session that persists across multiple requests.

**Presentation layer** handles data formatting, encryption, and compression before it reaches the application layer. This is where SSL/TLS operates in the stack. Understanding this explains why TLS is described as sitting between the transport and application layers.

Knowing all seven layers properly matters for understanding where attacks happen and what layer a given security control operates at.

## Room 4 — Packets, Frames, and Protocols

TCP/IP three-way handshake, UDP, and practical port exercises. The three-way handshake (SYN → SYN-ACK → ACK) was familiar but working through the practical exercise of watching it happen in real time made it concrete in a way that reading about it didn't.

The distinction between TCP and UDP from a security perspective:
- TCP's reliability makes it suitable for anything where data integrity matters — HTTP, SSH, FTP
- UDP's lack of connection state makes it exploitable for amplification attacks — DNS and NTP amplification DDoS attacks work because UDP doesn't verify the source address

Ports 101 reinforced that port numbers aren't just identifiers — they're the attack surface. Open ports are what scanners like nmap discover, and each open port is a potential entry point if the service behind it is vulnerable.

## Room 5 — Extending Your Network

Port forwarding, firewalls, VPNs, and LAN devices. The firewall practical was the most hands-on part — configuring rules and seeing which traffic gets blocked versus allowed. A firewall rule isn't just "block port 22" — it's a decision about source, destination, port, and protocol, and the order of rules matters because firewalls process them top to bottom.

VPNs were framed here from both a privacy and security angle. A VPN creates an encrypted tunnel between your device and the VPN server — all traffic appears to originate from the VPN server's IP. This shifts trust from your ISP to the VPN provider, which is worth understanding rather than treating VPNs as unconditionally private.

## What Networking Modules Add to Security Knowledge

Having done Packet Tracer labs before this, I came in with practical configuration experience. What TryHackMe adds is the attack surface perspective — every protocol has a security story. ARP has no authentication (ARP spoofing). DHCP has no rate limiting (starvation attacks). TCP has a predictable handshake (SYN flood). DNS trusts responses without verification (DNS poisoning).

The same networking knowledge that lets you build a network also tells you exactly where to attack it. That's the shift in perspective this module reinforced most clearly.

---
*TryHackMe username: sdenarzai786*
