---
title: "Week 3: Diving Deeper into Django's Internals"
date: 2025-03-07T12:00:00-05:00
draft: false
author: "Abe"
tags:
    - Django
    - Python
    - Open Source
    - Djangonaut Space
description: "A quieter week of exploration into Django's signing module codebase"
image: /images/blog/djangonaut-space-week-3.png
images: 
    - /images/blog/djangonaut-space-week-3.png
---

*This post is part of my journey through [Djangonaut Space](https://djangonaut.space/), a program helping developers contribute to Django. Follow along as I navigate through this adventure!*

## Quieter Week

Week 3 was definitely quieter. Just had less time to put into Django stuff this week... that happens sometimes I guess.

Still working on [PR #19191](https://github.com/django/django/pull/19191) for adding custom expiry to signed cookies.

## Going Deep into the Code

After our Tuesday meet, Ryan suggested using `git blame` to see why the code is written how it is. Super helpful tip!

So I did that - been trying to understand what's up with the `signing.loads()` function. Is it just a shortcut to `TimestampSigner` or is there some bigger design thing going on? 🤔

Hailey thinks it might be about how Python turns data into JSON (like for JavaScript). Makes sense why we'd be careful about changing it then.

## Code History Stuff

Found out most of the signing module was written 14 years ago in one [commit](https://github.com/django/django/commit/f60d42846365b2bf2f1c9bc7a3007c303122a20b) by some one named [Jannis Leidel](https://github.com/jezdez). Wild that it's barely changed since!

Oh and I noticed something interesting - when they added rotating keys support, they did change the signing module signature in [PR #15198](https://github.com/django/django/pull/15198). So there's at least some history of carefully extending the API.

## The Problem I'm Stuck On

Basically I have two options:

1. Add a new parameter to `loads()`
2. Make the signed cookies backend skip `loads()` and use `TimestampSigner` directly

Neither feels perfect. New parameters change the API which might break stuff. But skipping `loads()` might mess with how serialization works, according to what Hailey was saying.

Might just ask Jannis Leidel directly about what `loads()` was supposed to do originally. Would be cool to hear from the person who wrote it!

## What I Learned This Week 🧠

- Open source moves slow sometimes
- `git blame` is actually super useful
- Even tiny API changes need careful thinking
- Original authors can have useful insights even years later

Still pumped about this PR even if it's taking longer than I thought. Real coding isn't like the tutorials where everything works right away lol.

That's it for week 3! See ya next week!

---

*This post is part of my journey through [Djangonaut Space](https://djangonaut.space/), a program helping developers contribute to Django. Follow along as I navigate through this adventure!*
