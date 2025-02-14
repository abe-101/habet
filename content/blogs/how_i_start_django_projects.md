---
title: "How I Start Django Projects: My Go-To Setup"
date: 2025-02-13T12:00:00-05:00
draft: false
author: "Abe"
tags:
    - Django
    - Python
    - HTMX
    - Web Development
description: "Here's how I set up my Django projects after doing it about 5 times over the last few years"
image: /images/blog/how_i_start_django_projects.png
images: 
    - /images/blog/how_i_start_django_projects.png
---

I've been writing Django applications for about 2-3 years now, and I've started roughly 5 projects during that time. Every time I kick off a new project, I find myself going through the same setup steps that have worked well for me. I figured I'd share my process - it's not perfect, but it's been reliable and keeps me productive.

## Cookiecutter Django Is Your Friend

The first thing I do with any new Django project is fire up Cookiecutter Django. After setting up several projects, I've got my configuration down to a science. Here's what my typical setup looks like:

For the basics, I stick with:

- PostgreSQL 16 (always stay current)
- UTC timezone (essential for a business app)
- Email-based user authentication (simpler than username/password)
- Django Compressor for my frontend pipeline
- Sendgrid for emails (reliable and easy to set up)

I also always enable:

- Django REST Framework (because who doesn't need an API these days?)
- Celery (for background tasks)
- Sentry (catches those pesky production errors)
- Whitenoise (static file serving made simple)

For deployment, I go with:

- Heroku (quick to deploy, easy to maintain)
- AWS (for when I need more than what Heroku provides)
- GitHub Actions for CI

I skip Docker for local development - I prefer working directly with my local environment. I also don't bother with async Django - haven't really needed it yet.

One thing I always do is keep my `.env` files out of version control. Sure, it means a bit more setup time for new team members, but it's way safer.

## Making CSS Less Painful

I use Django LibSass with Django Compressor for my CSS workflow. The main reason I do this is to easily customize Bootstrap - especially theme colors and dark mode. It lets me override Bootstrap's SCSS variables without needing Node.js or any build tools.

The setup is pretty simple - just install `django-compressor` and `django-libsass`, add a few settings, and you're good to go. Django Compressor handles all the SCSS compilation automatically when the page loads.

I learned this approach from [Luke Plant's blog post](https://lukeplant.me.uk/blog/posts/django-sass-scss-without-nodejs-or-build-step/) and it's been working great for me. No npm, no webpack, no hassle - just working SCSS in Django.

## Dark Mode Because Why Not?

Bootstrap 5.3 comes with dark mode support out of the box, so I always add it to my projects. It's just a matter of adding `data-bs-theme="light"` to the base template and including some JavaScript to toggle between modes. I usually throw a theme toggle in the navigation bar using Bootstrap Icons.

I'm actually working on getting this merged into Cookiecutter Django as a default feature - you can check out the discussion in [this GitHub issue](https://github.com/cookiecutter/cookiecutter-django/issues/5629). Pretty soon you won't even have to set this up manually!

## HTMX Makes Things Nice

I always add HTMX to my projects now. It's a total game-changer with Django templates - you get modern interactivity without the complexity of a JavaScript framework. Just stick with Django templates and sprinkle in some HTMX attributes.

The setup is minimal - just install `django-htmx` and add the middleware. I even wrote a small package called [django-htmx-messages](https://github.com/abe-101/django-htmx-messages) that lets you use Django's message framework with HTMX responses. Perfect for toast notifications without page refreshes.

What I love about this setup is that I can build interactive features while keeping everything in Python and Django templates. No context switching to JavaScript required.

## Time Zones Are Important

I use TZ Detect for handling time zones in my Django projects. The approach is simple - store everything in UTC, but display times to users in their local timezone. The package handles all the detection and conversion automatically.

This has been a total game-changer for me. I recently built a parking payment system that operates across three different US time zones, and we haven't had a single timezone-related issue. The package (from [adamcharnock/django-tz-detect](https://github.com/adamcharnock/django-tz-detect)) just handles everything - it detects the user's timezone with JavaScript and configures Django accordingly.

When you're dealing with time-sensitive operations like parking payments across multiple time zones, getting this right from the start saves you from some major headaches down the road.

## Logging Setup

For logs, I go with Better Stack (Logtail). I set up JSON logs because they're easier to work with. Recently, I've been using Python's logger `extra` parameter to add user context to my logs - it's pretty neat. You can add things like user IDs or emails to your log entries, and then search through logs for specific users in Better Stack's interface.

```python
logger.info("User action", extra={"user_id": user.id, "email": user.email})

## Final Pieces

Then I wrap up with:

- Setting up the domain
- Deploying to Heroku
- Getting Sentry running
- Setting up SendGrid for email

That's my Django setup process. It might look like a lot at first glance, but it's become second nature after doing it a few times. Each piece serves a purpose and has saved me from headaches down the line.

I'm always tweaking things as I learn new tricks, but this setup has been solid for me. How do you set up your Django projects? I'd love to hear what works for others.

I'm always tweaking this process, but this is what's working for me right now. If you have your own way of setting up Django projects, I'd be curious to hear about it.
