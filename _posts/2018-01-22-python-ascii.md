---
layout: post
category: ""
title:  "python3 ascii问题"
tags: [python3]
---

python3遇到啊ascii问题，加上以下代码

```
import io
import sys

sys.stdout = io.TextIOWrapper(sys.stdout.buffer,encoding='utf-8')
```
