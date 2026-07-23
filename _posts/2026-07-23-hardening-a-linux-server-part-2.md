---
title: "Hardening a Linux Server From Scratch — Part 2: Fixing It and Proving It Worked"
date: 2026-07-23 15:44:00 +0500
categories: [Hands-On, Linux Security]
tags: [linux, hardening, ssh, firewall, ufw, sudo, vulnerability-assessment, beginner]
---

In [Part 1](/posts/assessing-a-linux-servers-security-baseline-part-1-enumeration-assessment/) I assessed a freshly deployed Ubuntu server and found five default-state weaknesses. This post is the other half: fixing each one *without locking myself out*, and then **proving** the fixes actually worked. Honestly, the "proving" part is where I learned the most — a config file will happily lie to you.

## Snapshot First, Always

Before touching a single setting I took a VM snapshot. The reason is simple: the changes I was about to make — firewall rules and SSH settings — are exactly the ones that can cut off my own access to the machine. A snapshot is a one-click undo if I brick it. This isn't optional; it's the difference between "oops, restore" and "reinstall the whole thing."

## The Fixes

Working through my findings from Part 1:

- **Firewall:** `ufw default deny incoming`, then **allow SSH before enabling**. The order matters enormously — if you run `ufw enable` before allowing port 22, you drop your own SSH connection instantly. Allow first, enable second.
- **Password expiry:** `sudo chage -M 90 denarzai` so the password can't live forever.
- **Login banner:** an authorized-use warning that shows on connect.
- **Disabled `multipathd`:** a storage-multipath daemon my simple single-disk VM has zero use for. Every running service is attack surface, so unused ones get turned off.
- **SSH lockdown:** set up key-based login, then disabled password authentication and root login entirely.

## The Thing That Made Me Stop and Think: Root Login vs sudo

When I disabled direct root login, something bugged me. Before, root could log in with a key. Now, to become root, I log in as a normal user and then use `sudo` — which asks for a password. My instinct was: *isn't that worse? Passwords can be brute-forced, keys can't.*

It took me a while to see why it's actually **stronger**, and it comes down to one idea: **a password can only be brute-forced if it's the thing standing at the door.**

My `sudo` password isn't at the door anymore — the SSH **key** is. To even reach the `sudo` prompt, an attacker has to *already* be logged in as my user, which now needs my private key. The password sits behind that key wall; it isn't reachable over the network at all. So the brute-force threat that applied to SSH password login simply doesn't apply to `sudo`.

And look at what an attacker now needs to get root:

- **Before:** one secret — a key. Steal it, get root instantly. Plus root is a known, directly-targetable account.
- **After:** two independent secrets — my **key** *and* my **password**. Steal the key and you only land in an unprivileged shell, still stuck at the `sudo` wall.

On top of that, every privileged action now runs through `sudo`, tied to a named user and logged — no more anonymous root sessions. Fewer ways in, more layers, full audit trail.

## The Gotcha That Almost Fooled Me: the Config That Lied

This is the part I'm most glad I caught. I set `PasswordAuthentication no` in `/etc/ssh/sshd_config`, reloaded SSH, and checked:

```bash
sudo sshd -T | grep passwordauthentication
# passwordauthentication yes   <-- still yes?!
```

Still **yes**. I'd edited the right file and reloaded — so why was password login still on? The answer: Ubuntu ships a drop-in file, `/etc/ssh/sshd_config.d/50-cloud-init.conf`, that also sets `PasswordAuthentication yes` — and that override wins over the main config.

```bash
sudo grep -ri passwordauthentication /etc/ssh/sshd_config.d/
# 50-cloud-init.conf:PasswordAuthentication yes
```

Fixed it there too, and only then did `sshd -T` report `no`. **The lesson: `sshd -T` shows the *effective* configuration — the values actually in force across every file — not just what's in the file you edited.** If I'd trusted the file I changed, I'd have walked away thinking password login was disabled while it was wide open. That's a genuinely dangerous kind of mistake, and it's exactly why you verify.

## Proving It Worked

Which brings me to verification. Making a change and *assuming* it worked isn't hardening — it's hoping. So I re-ran every check and compared before to after.

The strongest test wasn't reading a config value — it was proving the change from the **attacker's side**. I tried to log in with a password only:

```bash
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no denarzai@<target>
# Permission denied (publickey).
```

Rejected. And a normal login with my key still worked. That's real proof: password login is off, key login is on. A config value tells you your *intent*; a rejected login tells you the *reality*.

## What I'd Still Do

Things I'd add next if this were a real server:

- **A passphrase on my SSH key** — right now the key file alone is enough; a passphrase makes it "something I have + something I know."
- **Restrict SSH to my own IP** — instead of allowing port 22 from anywhere, only allow it from the admin's address, so the one exposed service isn't reachable by the whole internet.
- **`fail2ban`** to auto-block brute-force attempts, and **`unattended-upgrades`** for automatic security patches.
- **`DebianBanner no`** so SSH stops advertising its exact version.

## Takeaway

A freshly deployed server is not a secure one — hardening is something you do deliberately, in order, with a rollback ready. But the bigger lesson across both parts is this: **don't trust that a change worked — verify it, ideally from the attacker's side.** The config file can lie. The effective state and an actual rejected login don't.

That's my first full assessment done, start to finish: enumerate, assess, fix, verify, report. On to the next one.
