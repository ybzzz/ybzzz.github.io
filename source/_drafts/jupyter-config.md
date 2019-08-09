---
title: jupyter config
tags:
---

# jupyter notebook改变初始文件夹

打开cmd输入命令：`jupyter notebook --generate-config`
打开对应的py文件，搜索notebook_dir，将对应值改为：`c.NotebookApp.notebook_dir = 'F:\jupyterPro\文件夹路径'`，注意去掉注释用的#号。

打开jupyter notebook的属性，将目标中最后的路径`%USERPROFILE%/`改为`F:\jupyterPro\文件夹路径`，注意加上双引号，然后启动，就直接到达文件夹位置。