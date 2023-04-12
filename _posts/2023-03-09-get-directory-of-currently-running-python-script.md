---
layout: post
title: "Get directory of currently run python script"
date: 2023-03-09 07:00:00 -0000
categories: python
---

```python
import os

file_path = os.path.realpath(__file__)
directory_path = os.path.dirname(file_path)

print(directory_path)
```