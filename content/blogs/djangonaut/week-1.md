---
title: "Week 1: My First Django PR - Adding Custom Expiry to Signed Cookies"
date: 2025-02-21T12:00:00-05:00
draft: false
author: "Abe"
tags:
    - Django
    - Python
    - Open Source
    - Djangonaut Space
description: "My journey submitting my first pull request to Django, fixing a long-standing issue with custom expiry in the signed cookies session backend."
image: /images/blog/djangonaut-space-week-1.png
images: 
    - /images/blog/djangonaut-space-week-1.png
---

*This post is part of my journey through [Djangonaut Space](https://djangonaut.space/), a program helping developers contribute to Django. Follow along as I navigate through this adventure!*

## Finding My First Ticket

As a new Djangonaut, I was eager to dive into my first contribution to Django. But where do you start with such a large and established codebase?

I followed some excellent advice from [this YouTube short](https://www.youtube.com/shorts/D6QHet5U82U) about finding tickets suitable for newcomers. The key was to look for tickets that:

1. Already had a proposed patch but needed refinement
2. Required documentation updates
3. Hadn't been modified in some time

With guidance from our captain Ryan, I came across [ticket #27775](https://code.djangoproject.com/ticket/27775): "Signed cookies does not support custom expiry." This issue had been open for 8 years!

## Understanding the Problem

The issue was straightforward but important: Django's signed cookies session backend wasn't respecting the `set_expiry()` method. When you called this method on a signed cookie session, it simply did nothing - the call was silently ignored.

This was problematic for security reasons. Imagine you have a sensitive area of your application where you want sessions to expire quickly. If you set your default session timeout to a day but need a specific view to expire after 15 minutes, you'd expect `set_expiry(900)` to work - but with signed cookies, it wouldn't!

Looking at the code, I found this telling comment:

```python
# This doesn't handle non-default expiry dates, see #19201
```

Clearly, this was a known limitation, but it had never been addressed.

## The Solution Approach

The solution had several parts:

1. Modify the core `signing` module to support an `expiration_key` parameter
2. Update the `signed_cookies` session backend to use this new parameter
3. Add proper tests and documentation

Let's break down each part.

### Enhancing Django's Signing Module

The signing module is used by Django to cryptographically sign data. It already had timestamp-based expiration via `max_age`, but this was a global setting. We needed a way to embed an expiration timestamp within the signed data itself.

I added an `expiration_key` parameter to the `TimestampSigner.unsign_object()` and `loads()` functions. This parameter lets you specify a key in your data that contains an expiration time in seconds:

```python
def unsign_object(self, signed_obj, max_age=None, expiration_key=None, **kwargs):
    # First verify the outer max_age boundary
    value = super().unsign_object(signed_obj, max_age=max_age, **kwargs)
    
    # Then if expiration_key is provided, check it as an additional constraint
    if expiration_key is not None:
        expiry = value.get(expiration_key)
        if expiry is not None:
            self._verify_max_age(expiry)
            
    return value
```

This means you can now sign data like `{"username": "alice", "_session_expiry": 300}` and the signature will automatically expire after 300 seconds when using `expiration_key="_session_expiry"`.

### Updating the Signed Cookies Backend

With the core functionality in place, updating the session backend was simple. I just needed to add the `expiration_key` parameter to the `loads()` call:

```python
return signing.loads(
    self.session_key,
    serializer=self.serializer,
    max_age=self.get_session_cookie_age(),
    salt="django.contrib.sessions.backends.signed_cookies",
    expiration_key="_session_expiry",  # This is the new line
)
```

### Adding Tests

TODO

### Documentation Updates

Documentation is crucial! I added details about the new `expiration_key` parameter to the docs with a clear example:

```
.. class:: TimestampSigner(*, key=None, sep=':', salt=None, algorithm='sha256')
    
    .. method:: unsign_object(signed_obj, serializer=JSONSerializer, max_age=None, expiration_key=None,)

        Checks if ``signed_obj`` was signed less than ``max_age`` seconds ago,
        otherwise raises ``SignatureExpired``. The ``max_age`` parameter can
        accept an integer or a :py:class:`datetime.timedelta` object.

        If ``expiration_key`` is provided, it specifies a key in the signed data
        that contains an additional expiration time in seconds. This expiration
        acts as an additional constraint - the signature must be within both
        ``max_age`` (if provided) and the time specified in ``expiration_key``.
        For example, if ``max_age=30`` and the signed data contains
        ``{"foo": "bar", "exp": 10}``, the signature will expire after 10
        seconds when using ``expiration_key="exp"``.

        .. versionchanged:: 6.0

            The ``expiration_key`` parameter was added.

```

I made sure to include the `versionchanged` directive to indicate this is a new feature in Django 6.0.

## Submitting My PR

With all the code, tests, and documentation in place, I submitted [PR #19191](https://github.com/django/django/pull/19191).

I quickly received valuable feedback, particularly about handling non-dictionary values properly. One reviewer pointed out:

> Value here is allowed to be a "complex data structure (e.g. list, tuple, or dictionary)". Should support be added for non-dictionary types (perhaps supporting indexing?). Alternatively, should the error message be nicer if it is of the wrong type?

This was a great point! I responded by proposing a more graceful error handling approach:

```python
if not isinstance(value, dict):
    raise TypeError(
        f"expiration_key is only supported for dict values, not {type(value).__name__}"
    )
```

## Lessons Learned

This first PR taught me several valuable lessons:

1. **Understand the context**: Reading both the ticket history and related issues (#19201) gave me important context about the problem and previous attempts to solve it.

2. **Be thorough**: Solving the issue required touching multiple parts of Django - the core signing module, the session backend, tests, and documentation.

3. **Think about security**: Session expiration is a security feature, and fixing this bug helps developers implement proper security practices.

4. **Embrace the review process**: Getting feedback from experienced Django developers is invaluable - they catch edge cases and potential improvements I might have missed.

## What's Next?

I'm excited to continue my Djangonaut journey! For my next contribution, I'm looking for:

1. Another ticket that matches my skills
2. An opportunity to dive deeper into other areas of Django's internals
3. A chance to work on a more complex feature

Stay tuned for Week 2 of my Djangonaut Space adventure!

---

*This post is part of my journey through [Djangonaut Space](https://djangonaut.space/), a program helping developers contribute to Django. Follow along as I navigate through this adventure!*
