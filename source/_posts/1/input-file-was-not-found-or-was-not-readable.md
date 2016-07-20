---
title: Input file was not found or was not readable 解决方案
tags: [反编译]
---
apktool反编译时经常会出现下面的信息

1.Input file  was not found or was not readable.

2.Destination directory (C:\Users\user\a) already exists. Use -f switch if you want to overwrite it.

3.Exception in thread "main" brut.androlib.AndrolibException: Could not decode ars c fil..........

解决方案：

1和2是因为apktool升级到2.0以上时,使用方式已经替换,格式为:apktool d [-s] -f <apkPath> -o <folderPath>,参数具体的意思可以直接打apktool回车(windows)查看帮助

3是因为apktool版本过低导致,请升级到最新版本.[https://bitbucket.org/iBotPeaches/apktool/downloads](https://bitbucket.org/iBotPeaches/apktool/downloads)