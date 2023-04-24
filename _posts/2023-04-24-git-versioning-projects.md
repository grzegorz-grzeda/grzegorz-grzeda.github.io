---
layout: post
title: "Git versioning projects"
date: 2023-04-24 07:00:00 -0000
categories: git
---

## Premise

While developing a project usually I don't need to version it. The git history does all what I need. But when my project should be used somewhere else, I can't rely on commit-to-commit history. I need some versioning, so I can look at the numbers and deduce what consequences each version introduces.

## Solution

Here I came up (heavily influenced by [semantic versioning](https://semver.org/)) with this simple formula:

```
vMAJOR.MINOR.PATCH
```

examples:

```
v1.2.3
v10.2.3
v2.10.1232
```

## Explanations

I increment each number:
- `MAJOR` when the new version __introduces breaking changes__ in the project's __API__,
- `MINOR` when I extend __current API__ with __new__ functionality, __without__ breaking it (backwards compatible),
- `PATCH` when I fix errors or bugs backward compatibly.

I add the `v` prefix for git tagging purposes, not to start a tag name from a number.

## Application

I apply this versioning in project's:
- main `CMakeLists.txt` script
- `README.md`
- git history, as tags: `git tag -a vX.Y.Z -m "<comment>"` after the commit introducing the new version number

Now, if I use my project e.g. as a git submodule, I checkout to it's tagged version.

If I utilise some script to download a flat repo I also checkout to that git version tag.