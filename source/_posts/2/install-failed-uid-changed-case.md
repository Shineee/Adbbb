---
title: INSTALL_FAILED_UID_CHANGED 解决方案
tags: [编译部署]
---
**原因**：以前安装过这个应用程序，因为检查到原来/data/data下的文件对应uid还是旧的

**解决方案**：到/data/data下删除完这个程序包名对应的文件夹， 新安装的程序会另外生成uid。