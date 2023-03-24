# python

[TOC]

## python并发

```python
import threading

threadLock = threading.Lock()

def write_string(string, path="test.csv"):
    threadLock.acquire() # 加个同步锁就好了
    with open(path, 'a') as f:
        f.write(string + "\r\n")
    threadLock.release()

# 创建新线程
for i in range(15):
    thread1 = threading.Thread(target=write_string, args=["写入: " + str(i)]).run()
```

