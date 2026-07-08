---
title: "TryHackMe Pre-Security: Module 6 — How the Web Works"
date: 2026-07-08 08:23:00 +0500
categories: [Learning, TryHackMe]
tags: [tryhackme, dns, http, html, javascript, web-security, sql-injection, xss, beginner]
---

## Overview

Module 6 of TryHackMe's Pre-Security path covered how the web works across four rooms — DNS, HTTP, how websites are built, and a final room that tied everything together into one complete picture. This module was the most practically hands-on so far, with real exercises on actual lab machines rather than just reading theory.

## Room 1 — DNS in Detail

DNS record types, the domain hierarchy, and how a DNS request actually resolves from your browser to an IP address. The concepts were familiar from networking coursework — I've configured DNS in Cisco Packet Tracer and understand how resolution works at a high level. What this room added was depth on specific record types:

- **A record** — maps a domain to an IPv4 address
- **AAAA record** — maps a domain to an IPv6 address
- **CNAME** — maps a domain to another domain name
- **MX record** — specifies mail servers for a domain
- **TXT record** — free-form text, commonly used for domain verification and SPF/DKIM email authentication

The security angle: DNS is entirely unauthenticated by default. DNS poisoning (sending false DNS responses) and DNS hijacking (compromising the DNS server itself) are real attacks. DNSSEC exists to add cryptographic verification but adoption is incomplete.

## Room 2 — HTTP in Detail

HTTP methods, status codes, headers, and cookies — all implemented on TryHackMe's practice site so I could see real requests and responses rather than just reading about them. This was the most valuable room in the module because working on actual lab machines made the theory immediate.

HTTP methods and their security significance:
- **GET** — retrieves data. Parameters in the URL are visible and logged.
- **POST** — sends data in the request body. Still not encrypted without HTTPS.
- **PUT/DELETE** — modify and remove resources. Should require authentication.

Status codes tell you a lot during reconnaissance:
- `200` — success
- `301/302` — redirect (useful for discovering where a site routes traffic)
- `403` — forbidden (resource exists but you're not allowed)
- `404` — not found
- `500` — server error (sometimes reveals backend information)

Cookies are how HTTP maintains state across requests — HTTP itself is stateless, so the server issues a cookie after login and the browser sends it back with every subsequent request to prove identity. Session hijacking works by stealing this cookie.

## Room 3 — How Websites Work

HTML structure, JavaScript in the browser, sensitive data exposure, and HTML injection — all practiced on a live lab site. Two exercises stood out:

**Sensitive data exposure** — used the browser's View Source option to find credentials and API keys left in HTML comments or JavaScript files. This is a real finding in penetration tests — developers accidentally commit secrets to frontend code that anyone can read.

**HTML injection** — entered HTML tags into an input field that weren't sanitised by the server. The injected markup rendered in the page. This is the precursor to XSS (Cross-Site Scripting) — if you can inject HTML, the next question is whether you can inject `<script>` tags and execute JavaScript in another user's browser.

## Room 4 — Putting It All Together

This room tied every concept from the module into a single end-to-end picture and ended with a drag-and-drop quiz on the order of events when a browser requests a webpage. Working through the quiz forced me to think through the exact sequence rather than having a vague sense of it.

The complete chain when you visit a website:

1. Browser checks its local DNS cache
2. If not cached, queries the recursive DNS resolver
3. Resolver works up the hierarchy — root servers → TLD servers → authoritative nameserver
4. IP address returned to browser
5. Browser opens TCP connection to the server (three-way handshake)
6. If HTTPS, TLS handshake occurs
7. HTTP request sent
8. Request may pass through a WAF (Web Application Firewall) before reaching the server
9. Load balancer distributes the request to one of multiple backend servers
10. Web server processes the request, possibly querying a database
11. Response sent back through the chain
12. Browser renders HTML, fetches additional assets (CSS, JS, images) — possibly from a CDN
13. JavaScript executes on the client side

The new concepts from this room:

**Load balancers** sit in front of multiple servers, distributing traffic using algorithms like round-robin or least-connections. They also run health checks — if a server stops responding it's removed from rotation. Relevant to security: load balancers can mask which backend server you're actually hitting during a pentest.

**CDNs** host static assets (images, JS, CSS) from servers physically close to the user, reducing latency. From an attack perspective, CDN misconfigurations can expose origin server IPs.

**WAFs** analyse requests before they reach the web server, blocking known attack patterns, enforcing rate limits, and distinguishing bots from real browsers. Bypassing WAFs is a real skill in web application pentesting.

**Virtual hosts** allow one web server to host multiple domains by reading the `Host` header in the HTTP request and routing to the correct site. Virtual host enumeration is a technique for discovering hidden subdomains and internal applications.

## What This Module Solidified

This module connected the networking theory from Module 5 to the application layer where most web attacks actually happen. DNS, HTTP, cookies, and how the browser processes a page are the foundation for understanding SQL injection, XSS, CSRF, and every other web vulnerability covered in later modules. Seeing it as a complete chain rather than isolated concepts makes the attack surface much clearer.

---
*TryHackMe username: sdenarzai786*
