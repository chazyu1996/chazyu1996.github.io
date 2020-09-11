---
title: python的多线程数量控制
date: 2019-07-05T10:03:48+08:00
toc: true
tags:
  - python
categories:
  - 技术文章
---

python多线程如果不进行并发数量控制，在启动线程数量多到一定程度后，会造成线程无法启动的错误。
<!--more-->

```python
# -*- coding: utf-8 -*-
import threading
import Queue
import time


# 重写 python 的多线程类，使之与queue队列相结合
class Thread(threading.Thread):
    def __init__(self, queue, **kwargs):
        threading.Thread.__init__(self, **kwargs)
        self.queue = queue

    def run(self):
        super(Thread, self).run()
        self.queue.get()
        self.queue.task_done()


def test(x):
    print(x)
    time.sleep(x)
    print(x)


def main():
    q = Queue.Queue(20)
    for i in range(100):
        q.put(i)
        Thread(queue=q, target=test, args=(i,)).start()


if __name__ == '__main__':
    main()

```

