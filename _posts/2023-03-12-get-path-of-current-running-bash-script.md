---
layout: post
title: "Get directory of currently run bash script"
date: 2023-03-12 07:00:00 -0000
categories: bash
---

```bash
#!/bin/bash

SCRIPT_DIR=$( cd -- "$( dirname -- "${BASH_SOURCE[0]}" )" &> /dev/null && pwd )
```