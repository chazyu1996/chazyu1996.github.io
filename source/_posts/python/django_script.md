---
title: "编写django脚本，使用django的数据库连接"
date: 2019-07-05T10:03:48+08:00
toc: true
tags:
- python
categories:
  - 技术文章
---

针对django的项目，想编写脚本针对数据进行批量处理，通过导入以下几个包，即可编写python脚本来通过django的数据库连接操作
<!--more-->

```python
import os
import sys

import django

if __name__ == "__main__":
    sys.path.append("..")
    sys.path.append("{工程目录}")
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "saplatform.settings")
    django.setup()
    from django.contrib.auth.models import User
    # User数据操作

```

