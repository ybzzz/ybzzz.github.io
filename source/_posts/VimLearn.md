---
title: VimLearn
date: 2019-08-09 15:00:14
categories:
- Tools
tags:
- Editor
---

学习vimtutor记录。
<!--more-->

## normal model
1. hjkl ←↓↑→
2. x删除单个字符
3. A句尾添加
4. dw删除一个单词
5. d$删除从光标到行末；de删除从光标到单词末尾
`d motion`：d-删除操作符   motion-操作符的操作对象

6. 数字+motion  重复几次，如2w 光标移动两个单词；3e 光标移动至第三个单词末尾
7. d number motion
8. u撤销上次操作；U撤销整行操作
9. p将之前删除的文本放在光标后
10. rx使用x替换光标处字母（x为任意字符）
11. ce删除光标处单词并可插入
12. c number motion删除并插入； w(word) $(the end of the line)
13. ctr-g定位当前位置；G到文章末尾；gg到文章开头；number G 转到number行
14. /content 搜索content，?content反向搜索；n继续搜索；N相反方向搜索
15. ctr-o回到之前位置，ctr-i前向
