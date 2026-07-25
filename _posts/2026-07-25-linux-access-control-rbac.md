---
title: "Linux Access Control From Scratch: Users, Groups, Permissions & ACLs"
date: 2026-07-25 12:02:00 +0500
permalink: /posts/linux-access-control-rbac/
categories: [Hands-On, Linux Security]
tags: [linux, permissions, rbac, acl, users, groups, chmod, beginner]
---

The next task from the same task sheet was a different kind of problem: build an access-control structure for a company where employees are onboarding across departments — HR, Finance, IT — so each department can reach **only** its own resources. I built it on the same lab VM. I expected it to be a quick "create some users and folders" exercise. Instead it turned into the clearest lesson I've had on how Linux permissions *actually* work — mostly because of the things that confused me along the way.

## Step 1 — Users and groups (RBAC)

The professional way to manage access is **RBAC — Role-Based Access Control**: you don't hand permissions to individuals, you hand them to **groups** (roles) and drop users into groups. So I made a group per department (`hr`, `finance`, `it`), created a user for each, and added each user to their department group. One user (the IT admin) got `sudo`; the rest didn't.

```bash
sudo groupadd finance
sudo adduser sameer
sudo usermod -aG finance sameer
```

A gotcha I learned here: it's `usermod -aG`, and the **`-a` is not optional**. Without it, `-G` doesn't *add* the user to a group — it **replaces** all their existing supplementary groups with just the one you list, silently. Leave out the `-a` and you can accidentally remove someone from `sudo`. No error, just quietly broken.

## The thing that confused me: groups protect nothing by themselves

At this point I got stuck on a question: *why do I even need to make folders? I already have the groups.* It felt like the groups should already be doing the job.

They aren't — and this is the part worth internalizing. **A group is just a label — a named list of people. On its own it protects nothing and grants nothing.** Access control needs three separate pieces:

- **WHO** — the users and groups (the label)
- **WHAT** — the actual resource: the folder where a department's files live
- **RULES** — the permissions that connect a group to a resource

The way it finally clicked: the **group is a keycard**, the **directory is the room**, and the **permissions are the lock on the door**. A keycard opens nothing until there's a room with a matching lock. My groups were keycards for rooms that didn't exist yet.

So I built the rooms: `/data/hr`, `/data/finance`, `/data/it`, plus a `shared` and a `public` area — and set each department folder's **group-owner** to its department (`chown root:hr /data/hr`). That group-ownership is the real link between the label and the resource; the folder just happening to be *named* `hr` is only for humans.

## Owner, group, other — the one-bucket rule

The single most important concept: for any file or folder, a user is classified as **exactly one** of three, and only that bucket's bits apply:

```
-rwx rwx rwx
 │    │    │
owner group other
```

You're the **owner**, *or* you're in the file's **group**, *or* you're **other** — never a mix. I proved this by accident: before I locked things down (folders were the default `755`), a Finance user could read the HR folder. Not because he was in the HR group — he wasn't — but because he fell into **other**, and `755` gives "other" read+execute. That "other" access was exactly the hole I needed to close.

## Directory permissions are their own thing

`r`, `w`, `x` mean different things on a directory than on a file:

- **`r`** = list the names inside
- **`w`** = create or delete files inside
- **`x`** = *enter / traverse* it (`cd` in)

You need **`x`** to actually use a directory at all. So the modes I chose:

- Department folders + shared → **`770`** (`rwxrwx---`): the owning group gets full access, **other gets nothing**.
- Public → **`755`**: everyone can read and enter, only the owner can write.

## Proving it — and two errors that taught me something

Then I validated it by switching into each user (`su - <user>`) and testing both what should work **and** what should be blocked. Every user could write to their own department and to shared, and was denied everywhere else. Good.

But `touch` failed two *different* ways, and the difference turned out to be a lesson:

- On a folder I had **no** access to (`770`, I'm "other" → no `x`), `touch` said **`setting times ... Permission denied`** — I couldn't even *traverse* into the directory.
- On the public folder (`755`, I have `x` but no `w`), it said **`cannot touch ... Permission denied`** — I could get *in*, I just couldn't *create*.

So the exact error text told me which barrier stopped me: the door (`x`) or the write bit (`w`). That's the `x`-means-traverse idea showing up in real output.

A bonus check I didn't plan: when I tried `sudo` as a non-admin user, Ubuntu's `sudo-rs` literally replied *"I'm afraid I can't do that."* That confirmed only my IT admin was actually a sudoer — the privilege model, validated by the system refusing me.

## The surgical exception — ACLs

The standard model has a limit: a folder has exactly **one** group. So how do you give *one specific person* read access to Finance — say an auditor — without adding them to the Finance group (which would grant write and make them a permanent member) or opening the folder to everyone?

That's what **ACLs** are for:

```bash
sudo setfacl -m u:fatima:rx /data/finance
```

This grants *just* fatima *just* read+traverse, leaving the `770` base and the group untouched. `getfacl` shows the extra `user:fatima:r-x` line, and `ls -l` grows a `+` (`drwxrwx---+`) to signal "this has ACLs." I verified it: the auditor could now read Finance but still couldn't write, and nobody else was affected. A scalpel instead of a sledgehammer.

## One gap I'd still fix

The `shared` folder is `770` with no **sticky bit**, which means any staff member can delete *another* person's files there. On a real shared space I'd set `chmod 1770` (the sticky bit, like `/tmp`) so people can only delete their own files.

## Takeaway

Linux permissions look intimidating until you separate the three layers — **who** (users/groups), **what** (the resources), **rules** (owner/group/other bits, plus ACLs for exceptions). Groups don't secure anything on their own; they're labels waiting for a locked door to be attached to. Once that clicked, the whole model — and every `Permission denied` I hit — made complete sense.
