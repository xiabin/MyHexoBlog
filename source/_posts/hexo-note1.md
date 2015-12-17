title: hexo deploy 错误处理
date: 2015-12-17 18:03:58
categories: HEXO #文章文类
tags: [HEXO]
---

hexo 的错误信息处理
`hexo deploy`的时候遇到这样的错误信息

```java
Error: bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com': Invalid argument
```
解决办法就是把`hexo deploy`配置里面的 `repository` 改为以ssh提交的方式就解决了
