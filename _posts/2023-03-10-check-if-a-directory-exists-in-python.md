---
layout: post
title: "Check if a directory exists in python"
date: 2023-03-10 07:00:00 -0000
categories: code python
---

```
import os

# Specify path
path = '/home/me/file.txt'
 
# check if path exists
isExist = os.path.exists(path)
print(isExist)
```