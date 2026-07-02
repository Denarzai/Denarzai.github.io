---
title: "TryHackMe Pre-Security: Module 2 — Introduction to IT & Computing"
date: 2026-07-02 05:09:00 +0500
categories: [Learning, TryHackMe]
tags: [tryhackme, networking, cloud, virtualization, operating-systems, beginner]
---

## Overview

Module 2 of TryHackMe's Pre-Security path covered the foundational concepts of IT and computing across four rooms. Most of this was conceptual rather than hands-on — building the mental model of how computer systems are structured and how they communicate before getting into security specifics.

## Room 1 — Core Computer Components and the Boot Process

The first room covered what's physically inside a computer and how it boots up. The key takeaway wasn't the individual components but understanding that **the boot process is a frequent attack target**. Before the operating system is fully loaded there's a window where the system is in a relatively unprotected state — BIOS/UEFI, bootloaders, and early kernel initialisation are all points where malware can embed itself to survive reboots and evade detection.

Knowing how a system starts is foundational for understanding persistence mechanisms in malware analysis and incident response.

## Room 2 — Types of Computer Systems

Eight types of computers were covered — from personal computers and servers to embedded systems and IoT devices. The most interesting point: **the most critical computers are often the least visible**. Embedded systems control physical infrastructure — doors, aircraft systems, industrial equipment — but they're often poorly secured because security wasn't a design priority when they were built.

This connects directly to ICS/SCADA security, which is one of the more specialised areas of cybersecurity and a growing field as critical infrastructure becomes more networked.

## Room 3 — The Client-Server Model and HTTP

This room covered how devices on the internet offer services to each other. The client-server model works like a restaurant — the client sends a request, the server processes it and responds.

The HTTP protocol was used as the example. What actually happens behind the scenes when a browser requests a webpage:

1. Client sends an HTTP request with a method (GET, POST), headers, and optionally a body
2. Server processes the request and returns a response with a status code (200 OK, 404 Not Found, etc.) and the content

This is directly relevant to web application security — SQL injection, XSS, CSRF, and most web vulnerabilities exploit this request-response cycle. Understanding what a raw HTTP request looks like is the first step to understanding how these attacks work.

## Room 4 — Virtualization and Cloud Computing

Two rooms covered this topic. The key concepts:

**Virtualization** allows one physical machine to run multiple isolated virtual machines. A hypervisor manages the VMs, allocating CPU, memory, and storage. Containers are a lighter version — instead of a full virtual machine, containers share the host OS kernel but isolate the application running inside.

**Cloud computing** applies these concepts at scale. The three service models:

- **IaaS** — you rent raw infrastructure (servers, storage). You manage the OS and everything above it.
- **PaaS** — you get a ready environment to deploy applications. The provider manages the infrastructure.
- **SaaS** — you use software directly. Gmail, Zoom, Google Docs.

From a security perspective, the shared responsibility model matters here. In IaaS the user is responsible for securing the OS, applications, and data. In SaaS the provider handles almost everything but the user is still responsible for access control and data governance.

Containers are particularly relevant in cybersecurity — container escape vulnerabilities allow an attacker who compromises a container to break out into the host system, which is a known and actively exploited attack path.

## What Was New vs What I Already Knew

Most of the networking and client-server concepts were familiar from my networking coursework using Cisco Packet Tracer. What was new was seeing cloud computing broken down into IaaS, PaaS, and SaaS explicitly, and understanding the security implications of each model.

The boot process section was new in terms of security framing — I knew what BIOS did but hadn't thought about it as an attack surface before.

## What's Next

Module 3 covers networking fundamentals — how data actually moves across networks, DNS, HTTP in more depth, and how the internet works at the protocol level. This is where the conceptual groundwork from this module starts becoming practically applicable.

---
*TryHackMe username: sdenarzai786*
