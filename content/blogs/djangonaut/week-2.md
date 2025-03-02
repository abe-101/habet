---
title: "Week 2: Finding More Ways to Help Django"
date: 2025-02-28T12:00:00-05:00
draft: false
author: "Abe"
tags:
    - Django
    - Python
    - Open Source
    - Djangonaut Space
description: "Looking beyond code contributions to help the Django community"
image: /images/blog/djangonaut-space-week-2.png
images: 
    - /images/blog/djangonaut-space-week-2.png
---

*This post is part of my journey through [Djangonaut Space](https://djangonaut.space/), a program helping developers contribute to Django. Follow along as I navigate through this adventure!*

## More Ways to Contribute

Started off week 2 looking for other ways to contribute to Django beyond just code. Turns out there's a ton!

I looked into the "Updates to Django" section in the Django newsletter. Found a [detailed guide](https://www.dropbox.com/scl/fi/iw9wqv7wqttx1cvpgzdly/Writing-Updates-to-Django-Section.paper?rlkey=0f9raorlwfj245nkkez4w4ef4&dl=0) on how it works.

Basically:

- Once a week someone runs a short script using the GitHub CLI
- It finds all PRs merged into Django that week
- Points out first-time contributors 🎉
- Then the author adds anything else interesting in the Django world

Might help out with this in the future!

Also had an idea - there's no good way to remind the weekly author it's their turn. I suggested making a Discord bot that could send reminders. Could be a small but useful project.

## PR Update

My PR from last week got reviewed by Sarah Boyce. Main feedback: we should really try to avoid API changes. Makes sense - Django is used by so many people that even small changes can cause problems.

Going to rethink my approach and see what can be done without changing the API.

## What I Learned This Week 🧠

- How the weekly Django Updates newsletter section gets written 📝
- Contributing to open source is a slow and thorough process
- API stability is super important in mature frameworks
- You can attribute someone else in a commit using `Co-authored-by`! GitHub will show their contribution

That last one is important for my PR since a lot of the code was originally created by [buugaj](https://github.com/buugaj). Raffaella pointed this out in Discord and wrote a [blog post](https://raffaella.bearblog.dev/co-author-a-commit-with-pycharm/) about how to do it in PyCharm. Definitely going to use this!

That's it for week 2! Slower than I expected, but I'm learning that's just how open source works - quality over speed.

Stay tuned for Week 3 of my Djangonaut Space adventure!

---

*This post is part of my journey through [Djangonaut Space](https://djangonaut.space/), a program helping developers contribute to Django. Follow along as I navigate through this adventure!*
