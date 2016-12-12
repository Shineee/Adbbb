---
title: 删除 Github 上某个文件的历史记录
tags: [Git]
---
**问题**：删除 Github 上某个文件的历史记录

**解决方案**：

```shell
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch 文件名|文件夹/文件名' --prune-empty --tag-name-filter cat -- --all
git push origin master --force
rm -rf .git/refs/original/
git reflog expire --expire=now --all
git gc --prune=now
git gc --aggressive --prune=now
```

**注**：文件名|文件夹/文件名，举个例子

file.txt

folder/file.txt

其他的按步骤复制，依次执行。

