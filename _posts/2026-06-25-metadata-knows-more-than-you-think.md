---
title: "Metadata Knows More About You Than You Think"
date: 2026-06-25 10:00:00 +0500
categories: [Learning, CS50 Cybersecurity]
tags: [privacy, metadata, cookies, tracking, dns, fingerprinting, beginner]
---

The privacy lecture in CS50 Cybersecurity was the one that hit closest to home. Not because it covered exotic attacks or nation-state hacking — but because it described things happening to me right now, every day, without my realising the full picture.

The central lesson: **metadata is not harmless**.

## What Metadata Actually Is

Content is what you say. Metadata is everything around it.

For a phone call, the content is the conversation. The metadata is: who you called, when, for how long, from which cell tower. You might think that's harmless compared to the actual words. But consider what metadata alone reveals:

- You called a cancer helpline three times this week
- You called a divorce lawyer, then your bank, then your sister
- You called the same number at 2am every night for a month

Nobody heard a word you said. But the metadata tells a complete story.

This is exactly what Salt Typhoon collected. Chinese state hackers breached US telecoms and accessed call detail records — CDRs — for over a million people in the Washington DC area. No audio needed. Just metadata. From that data alone, you can map the relationships, schedules, and priorities of government officials. That's a counterintelligence goldmine.

## How Websites Track You

Websites use several mechanisms to build profiles on visitors. I went through each one in the course and came out with a much clearer picture of what actually happens when I open a browser.

**Cookies** are small files a website stores on your device. First-party cookies are set by the site you're visiting — your login session, your shopping cart. Third-party cookies are set by someone else, like Google or Meta, whose code runs on the page. When you visit any of the thousands of sites running Google Analytics, Google places a tracking cookie on your device. The next site you visit with Google Analytics — they know it's you. Over time they build a detailed profile across every site you've visited.

**Tracking parameters** are the `?utm_source=...` strings you see in URLs. When you click a link from an email or ad, these parameters tell the destination site where you came from. They're also used to link your identity across platforms.

**Browser fingerprinting** is more invasive than cookies and harder to block. Your browser leaks information about your system — screen resolution, installed fonts, timezone, browser version, hardware specifications. Combined, these create a fingerprint that's unique to your device. Unlike cookies, you can't clear your fingerprint. Incognito mode doesn't help. Even a VPN doesn't change it.

## DNS — The Part Nobody Thinks About

Every time you visit a website, your device first asks a DNS server to translate the domain name into an IP address. By default, this request goes to your ISP's DNS server — unencrypted, in plaintext.

Your ISP can see every domain you look up. Not the pages you visit within a site, but every domain. That's a comprehensive log of your internet activity.

**DNS over HTTPS (DoH)** encrypts DNS queries so they're indistinguishable from regular HTTPS traffic. Your ISP can no longer read them. Firefox and Chrome both support DoH. It's one of the easiest privacy improvements you can make.

**DNS over TLS (DoT)** does the same thing but on a dedicated port, which means it's easier for network administrators to block — relevant if you're on a corporate network that wants to monitor DNS.

## What Incognito Mode Actually Does

Almost nothing useful for privacy. Incognito mode:
- Doesn't save your browsing history locally
- Doesn't save cookies after the session ends
- Does NOT hide your activity from your ISP
- Does NOT hide your activity from websites
- Does NOT change your browser fingerprint

It's useful for logging into a second account or keeping a surprise gift from appearing in autofill. It is not a privacy tool. Your ISP, your employer if you're on their network, and the websites you visit all still see everything.

## The Tools That Actually Help

After going through this lecture, here's what I actually changed:

- **Signal** for calls and messages — end-to-end encrypted, metadata protected better than standard calls
- **DNS over HTTPS** enabled in Firefox — ISP can no longer log my DNS queries
- **uBlock Origin** — blocks third-party tracking scripts at the source
- Understanding that a VPN just moves trust from your ISP to the VPN provider — it's not magic

## Key Takeaways

- Metadata reveals as much as content — sometimes more
- Third-party cookies track you across the entire web, not just one site
- Browser fingerprinting bypasses cookie-clearing and incognito mode
- DNS queries are plaintext by default — DoH fixes this
- Incognito mode protects your local history, not your network activity
- The Salt Typhoon hack collected metadata, not recordings — and that was enough
