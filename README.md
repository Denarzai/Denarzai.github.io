# denarzai.github.io

My personal cybersecurity blog, live at **[denarzai.github.io](https://denarzai.github.io)**.

I'm a BS Cybersecurity student at FAST NUCES Islamabad, and this blog documents everything I learn on the way to becoming a security professional — CTF walkthroughs, course reflections, and plain-language explanations of security concepts.

## What's on the blog

- **OverTheWire Bandit series** — step-by-step walkthroughs of every level I've completed (Level 0 through Level 21 and counting), covering Linux fundamentals, SSH, file permissions, cron jobs, setuid binaries, and privilege escalation
- **TryHackMe notes** — writeups from the Pre-Security and Jr Penetration Tester paths
- **Concept posts** — how passwords are really stored, why HTTPS is not enough, SQL injection in the real world, what file metadata reveals about you
- **Course reflections** — Harvard CS50 Cybersecurity, including my final project on the Salt Typhoon APT campaign

## Tech

Built with [Jekyll](https://jekyllrb.com/) and the [Chirpy theme](https://github.com/cotes2020/jekyll-theme-chirpy), deployed automatically to GitHub Pages via GitHub Actions on every push.

### Run locally

```bash
git clone https://github.com/Denarzai/Denarzai.github.io.git
cd Denarzai.github.io
bundle install
bundle exec jekyll serve
```

Then open `http://127.0.0.1:4000`.

### Write a new post

Add a Markdown file to `_posts/` named `YYYY-MM-DD-title.md` with Chirpy front matter:

```yaml
---
title: "Post Title"
date: YYYY-MM-DD HH:MM:SS +0500
categories: [CTF, Bandit]
tags: [linux, ssh]
---
```

## License

Content and configuration published under the [MIT License](LICENSE). Theme by [cotes2020/jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy).
