---
title: "SQL Injection: The 25-Year-Old Vulnerability Still Breaking Things"
date: 2026-06-23 10:00:00 +0500
categories: [Learning, CS50 Cybersecurity]
tags: [sql-injection, web-security, securing-software, owasp, beginner]
---

SQL injection has been on the OWASP Top 10 list of most critical web vulnerabilities for over two decades.

## What SQL Injection Actually Is

Every website that stores data — user accounts, posts, orders, anything — uses a database. The most common type is a SQL database, which you interact with using queries. A login form, for example, generates a query like this behind the scenes:

```sql
SELECT * FROM users WHERE username = 'subhan' AND password = 'mypassword';
```

This asks the database: "find me a user where the username is subhan and the password is mypassword." If it finds one, you're logged in.

Now here's where it breaks. What if instead of typing `subhan` as my username, I type this:

```
' OR '1'='1
```

The query becomes:

```sql
SELECT * FROM users WHERE username = '' OR '1'='1' AND password = '';
```

The condition `'1'='1'` is always true. So the database returns a user — often the first one in the table, which is usually the admin. I just logged in without a password.

That's SQL injection. User input is being treated as part of the SQL command itself rather than just data. The attacker didn't break any encryption or bypass any firewall. They just typed something unexpected into a text box.

## A Real Example — Cisco IOS XE

The vulnerability I covered in my CS50 final project — CVE-2023-20198 — was essentially an authentication bypass through unexpected input, similar in spirit to SQL injection. The web management interface of Cisco routers didn't properly validate a specific HTTP request, allowing unauthenticated users to create admin accounts.

The underlying lesson is identical: never trust input. Whether it's a login form, a URL parameter, or an HTTP header — if user-controlled data touches your application logic without validation, you have a vulnerability.

A more direct SQL injection example is the **MOVEit breach of 2023**. CVE-2023-34362 was a SQL injection vulnerability in MOVEit Transfer, a widely used file transfer tool. The Clop ransomware group exploited it to steal data from hundreds of organisations including British Airways, the BBC, and the NHS. All from a single SQL injection flaw.

## What Prevents It — Prepared Statements

The fix is called a **prepared statement** (also called a parameterised query). Instead of building the SQL query by concatenating strings, you define the query structure first and pass the user input separately:

```python
cursor.execute("SELECT * FROM users WHERE username = ? AND password = ?", (username, password))
```

The database now knows the structure of the query before it sees the user input. Whatever the user types gets treated as a literal string — data, not code. Typing `' OR '1'='1` just searches for a user with that exact username. No injection possible.

This is the difference between `eval()` and safe parsing — a concept that applies across every programming language. If you ever find yourself building executable code by concatenating user input, stop.

## Other Injection Variants

SQL injection is the most famous but the same principle applies elsewhere:

- **Command injection** — user input gets passed to a system shell command
- **LDAP injection** — user input manipulates directory service queries
- **XPath injection** — user input manipulates XML queries

The pattern is always the same: input that was meant to be data gets interpreted as code. The fix is always the same: separate data from code through parameterisation or escaping.

## Why It's Still Everywhere

I was surprised to learn how common SQL injection still is. The reason isn't that developers don't know about it — it's covered in every web development course. The reason is scale. A large application might have thousands of input points. One developer on one tired afternoon writes a query without a prepared statement. The rest of the application is fine. That one endpoint is the breach.

This is why security testing — specifically tools like SQLMap that automated injection testing — is a standard part of any penetration test. You don't assume the code is clean. You verify it.

## Key Takeaways

- SQL injection treats user input as executable code instead of data
- A single vulnerable endpoint in an otherwise secure application is enough
- Prepared statements are the correct fix — input validation alone is insufficient
- The same principle applies to command injection, LDAP injection, and others
- MOVEit 2023 shows this vulnerability is still causing major breaches today
