---
title: "Use Pre-Commit to Include OpenAPI Schema in Version Control"
date: 2024-04-17T23:26:54-05:00
draft: false
author: "Abe"
tags:
    - Django
    - OpenAPI
    - Pre-commit
description: "Use Pre-Commit to Include OpenAPI Schema in Version Control"
image: /images/pre-commit-terminal.png
images: 
    - /images/pre-commit-terminal.png
---

## The Problem

Lately, I've been exploring the idea of including a project's OpenAPI schema
under git version control.

This is particularly useful with tools like Postman, where you can have a
collection point to a remote schema on GitHub, and it will automatically update
the collection when the schema changes.

For this to work, the schema file must be generated every time the API changes.
This can easily be forgotten when done manually.

## Background

I am currently working on a Django project for a client that uses Django Rest
Framework to publish an API. drf-spectacular is a great complementary package
that generates documentation for the API using either SwaggerUI or ReDoc.

drf-spectacular also publishes an endpoint that returns the project's OpenAPI
schema. However, the documentation and schema are restricted to only specific
users, and tools like Postman don't provide a way to authenticate to access the
schema.

## Solution

To address this, I decided to include the schema in the project sources and
make it available to anyone who has access to the project's repository. You can
then grant Postman access to the private repository, and it will be able to
access the schema.

## How to Do It

In order to achieve this, I decided to use pre-commit to generate the schema.
Here is a snippet from the `.pre-commit-config.yaml` file:

```yaml
default_language_version:
  python: python3.11

repos:
  - repo: local
    hooks:
    -   id: generate-openapi-schema
        name: Generate OpenAPI Schema
        entry: bash -c 'python manage.py spectacular --color --file schema.yml --validate --fail-on-warn && git add schema.yml'
        language: system
        always_run: true
        pass_filenames: false
        stages: [commit]
```

The above snippet will run the generate-openapi-schema hook every time you commit.
