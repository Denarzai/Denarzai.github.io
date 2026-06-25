---
title: "How Passwords Are Actually Stored — And Why It Matters"
date: 2026-06-19 10:00:00 +0500
categories: [Learning, CS50 Cybersecurity]
tags: [hashing, encryption, passwords, cryptography, beginner]
---

I just finished Harvard's CS50 Cybersecurity course, and one of the first things that genuinely surprised me was how passwords work under the hood. I always assumed websites stored your password somewhere and checked it when you logged in. Turns out that's exactly the wrong way to do it — and plenty of companies have learned that the hard way.

## What I Thought Happened

I assumed a website worked like this:

1. You create an account with the password `hunter2`
2. The website stores `hunter2` in a database
3. When you log in, it checks what you typed against what it stored

This is called **plaintext storage** and it's a disaster. If an attacker breaks into the database, they have everyone's actual password. And since most people reuse passwords, one breach gives them access to dozens of accounts across different sites.

## What Actually Happens — Hashing

Instead of storing your password, a properly built system stores a **hash** of your password.

A hash is the output of a one-way mathematical function. You put your password in, you get a fixed-length string of characters out. The important part: you cannot reverse it. You can't look at the hash and work backwards to find the password.

So when you create an account with `hunter2`, the website:
1. Runs `hunter2` through a hash function like SHA-256 or bcrypt
2. Stores the output — something like `f52fbd32b2b3b86ff88ef6c490628285f482af15ddcb29541f94bcf526a3f6c7`
3. Throws away the original password entirely

When you log in, it hashes what you typed and compares the two hashes. If they match, you're in. Your actual password never gets stored anywhere.

## The Problem With Basic Hashing — Rainbow Tables

Here's where it gets interesting. If hashing is one-way, how do attackers crack passwords?

With a **rainbow table**. Since common passwords like `password123` always produce the same hash, attackers precompute massive tables mapping passwords to their hashes. If your hashed password appears in their table, they've cracked it instantly — no reversing the math required.

This is exactly what happened in the LinkedIn breach of 2012. LinkedIn was hashing passwords with SHA-1 but not doing the next step. 6.5 million hashed passwords were leaked. Attackers cracked a huge portion of them within hours using precomputed tables.

## The Fix — Salting

A **salt** is a random string added to your password before hashing. So instead of hashing `hunter2`, the system hashes `hunter2` + `x7kQ9mZ2` (the random salt), then stores both the hash and the salt.

Now rainbow tables are useless. Even if two users have the same password, their salts are different, so their hashes are completely different. The attacker can't use precomputed tables — they'd have to brute-force each password individually, which takes vastly more time.

Modern systems like bcrypt and Argon2 handle salting automatically and are deliberately slow to compute — which matters because it makes brute-forcing expensive even with powerful hardware.

## The Real World Lesson

The Salt Typhoon attack I studied — where Chinese hackers breached nine US telecoms — involved accessing call records and intercepted communications. But poor credential security is often the first step in any breach. Once attackers have valid credentials, everything else gets easier.

What I took from this: the gap between "technically working" and "actually secure" is enormous. A website that stores hashed passwords technically works. A website that uses bcrypt with salting is actually secure. That difference matters when millions of users are counting on you.

## Key Takeaways

- Never store plaintext passwords — hash them
- Basic hashing alone is vulnerable to rainbow tables
- Salting makes each hash unique even for identical passwords
- Use slow hash functions like bcrypt or Argon2, not fast ones like MD5 or SHA-1
- MD5 and SHA-1 are broken for password storage — they're too fast and have known weaknesses
