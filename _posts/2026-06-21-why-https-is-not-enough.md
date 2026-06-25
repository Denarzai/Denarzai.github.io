---
title: "Why HTTPS Is Not Enough — What Your Browser Doesn't Tell You"
date: 2026-06-21 10:00:00 +0500
categories: [Learning, CS50 Cybersecurity]
tags: [https, tls, encryption, network-security, mitm, beginner]
---

Before I took CS50 Cybersecurity, I thought the padlock icon in my browser meant I was safe. A site either had HTTPS or it didn't, and if it did, everything was fine. That turned out to be one of the most common misconceptions in everyday security — and understanding why changed how I think about browsing entirely.

## What HTTPS Actually Does

HTTPS stands for HTTP Secure. The "secure" part comes from **TLS** — Transport Layer Security — a protocol that encrypts the connection between your browser and the website's server.

When you visit `https://gmail.com`, here's what happens before you see a single pixel:

1. Your browser and Google's server perform a **TLS handshake**
2. They agree on an encryption method and exchange keys
3. Everything sent between them from that point — your emails, your password, everything — is encrypted

The key point: TLS protects data **in transit**. While it's travelling across the network, nobody intercepting it can read it.

## What HTTPS Does NOT Do

This is where most people's mental model breaks down.

HTTPS does not mean the website is trustworthy. It means the connection is encrypted. A phishing site designed to steal your banking credentials can have a valid HTTPS certificate. The padlock just means nobody between you and the fake site can eavesdrop — it says nothing about whether the site itself is malicious.

HTTPS also does not hide **metadata**. Even over an encrypted connection, your ISP can see:
- Which domains you're connecting to
- When you connected
- How much data was transferred

They can't see what you typed or what pages you viewed within a site. But they know you visited `mentalhealth.gov` at 2am for 45 minutes. That metadata alone tells a story.

## Man-in-the-Middle Attacks

Before HTTPS became standard, a common attack on public WiFi was the **man-in-the-middle (MITM) attack**.

Imagine you're at a café connecting to the WiFi. An attacker on the same network positions themselves between you and the internet. Every request you send goes through them first. On unencrypted HTTP sites, they can read and modify everything — inject malicious scripts, steal credentials, change what you see.

TLS makes this significantly harder because the attacker can't read the encrypted traffic. But a sophisticated attacker can attempt **SSL stripping** — downgrading your HTTPS connection to HTTP before you notice. This is why browsers now flag non-HTTPS sites aggressively and why **HSTS** (HTTP Strict Transport Security) exists — it tells your browser to never accept HTTP from a particular domain.

## The Certificate Authority Problem

How does your browser know the TLS certificate it receives is genuine? Through **Certificate Authorities** — companies like DigiCert, Let's Encrypt, and Comodo that your browser trusts by default.

When a website gets an HTTPS certificate, a CA verifies they own the domain and signs the certificate. Your browser checks this signature against its list of trusted CAs.

The weak point: if a CA is compromised or issues a certificate incorrectly, an attacker can impersonate any website. This happened in 2011 when DigiNotar, a Dutch CA, was breached. Attackers issued fraudulent certificates for Google, allowing Iranian intelligence to intercept Gmail traffic of hundreds of thousands of users. DigiNotar went bankrupt shortly after.

## What This Connects to in the Real World

In the Salt Typhoon attack — where Chinese state hackers compromised US telecoms — one of the core issues was that standard phone calls have no equivalent of TLS. Calls travel through the carrier's infrastructure without end-to-end encryption. CALEA legally requires carriers to maintain access to call content.

Signal uses end-to-end encryption, which is like HTTPS but stronger — not even Signal's servers can decrypt your messages. CALEA cannot compel access to content that the platform itself cannot read. That's why the FBI has publicly criticised encrypted messaging apps — from a law enforcement perspective, E2E encryption is the problem. From a security perspective, it's the solution.

## Key Takeaways

- HTTPS encrypts your connection, not the website's trustworthiness
- Metadata — who you talk to, when, how long — is still visible to your ISP
- Man-in-the-middle attacks are the threat HTTPS is designed to counter
- Certificate Authorities are a trust hierarchy with real failure modes
- End-to-end encryption goes further than HTTPS — not even the server can read the content
