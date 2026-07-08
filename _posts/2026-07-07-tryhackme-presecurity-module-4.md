---
title: "TryHackMe Pre-Security: Module 4 — Data Representation & Programming Basics"
date: 2026-07-07 14:17:00 +0500
categories: [Learning, TryHackMe, Pre-Security]
tags: [tryhackme, binary, unicode, python, javascript, sql, beginner]
---

## Overview

Module 4 of TryHackMe's Pre-Security path covered five rooms — number systems, data encoding, Python basics, JavaScript basics, and SQL fundamentals. This module was less about security directly and more about building the foundational technical literacy that underpins everything else.

## Room 1 — Number Systems

Binary, hexadecimal, octal, and decimal were covered along with how colors are represented in memory. Binary and hex were already solid for me — they come up constantly in assembly language work and understanding memory layouts. The one thing that was genuinely new was how colors are stored: one byte each for red, green, and blue, giving 256 possible values per channel and over 16 million color combinations total. That explains why hex color codes like `#FF5733` are structured the way they are — two hex digits per color channel.

## Room 2 — Data Encoding

ASCII and its limitations, then Unicode and its three encoding standards — UTF-8, UTF-16, and UTF-32. ASCII was familiar but Unicode encoding was entirely new territory.

The core problem ASCII has: it only covers 128 characters, designed for English. UTF-8 solves this by using variable-length encoding — common characters like ASCII use 1 byte, less common characters use 2-4 bytes. This is why UTF-8 became the dominant standard for the web — it's backward compatible with ASCII and efficient for English text while still supporting every language, symbol, chess piece, and emoji in existence.

UTF-16 uses 2 bytes minimum, UTF-32 uses exactly 4 bytes per character — simpler to process but wasteful for primarily ASCII content. Understanding encoding matters in security because encoding mismatches and improper handling of Unicode input are real attack vectors in web applications.

## Room 3 — Python Basics

Variables, conditionals, and loops demonstrated through a simple Python program. Already comfortable with C++ so the logic transferred easily — the syntax is just cleaner and less verbose. Python is the language I'll be using most for security scripting going forward so this was a useful orientation even if the concepts themselves weren't new.

## Room 4 — JavaScript Basics

Same three concepts as Python but in JavaScript. The syntax differences required more attention — `console.log()` instead of `print()`, `let` and `const` for variable declaration, the general structure around functions and control flow. JavaScript is important for web application security — understanding how it works on the client side is foundational for XSS, DOM manipulation attacks, and reading vulnerable code.

## Room 5 — SQL Basics

SQL was the most substantive new content in this module. A complete new language — how relational databases store data in tables with rows and columns, and how to query them:

- `SELECT` — choose which columns to display
- `FROM` — specify the table
- `WHERE` — filter rows by condition
- `ORDER BY` — sort results

What made this click was seeing how this connects to SQL injection, which I covered in the CS50 cybersecurity course. The reason SQL injection works is exactly because user input gets treated as part of the SQL query itself rather than as data. Understanding the language means understanding why the attack is possible and what makes parameterised queries the correct fix.

The room ends with a question worth thinking about: what could happen if someone were allowed to change or remove records without permission? In a real database that's not a hypothetical — it's the impact of a successful SQL injection attack.

## Overall

This module was foundational rather than revelatory — the value was in solidifying concepts and seeing how they connect to security. Number systems and encoding matter for understanding binary analysis and web vulnerabilities. Python and JavaScript are the two most common languages in security tooling and web application attacks respectively. SQL is directly exploitable in its most common attack form. None of this exists in isolation.

---
*TryHackMe username: sdenarzai786*
