---
layout: post
title: "Android Notepad Tutorial 1"
description: ""
category: life
tags: [Android, Beginner, Mobile Dev]
---
{% include JB/setup %}

一直想做 mobile dev，但是囊中羞涩 Apple 一年99刀实在有点贵，只好找免费的 Android 练手，但是没想好想做什么，于是每每半途而废。正好前段时间想找一个 todo 工具监督下自己的一些日程安排，尝试了 todo list 和 Wunderlist，都有一些不能完全合乎自己要求的地方，想满足要求就得掏钱买高级版。于是就这么走上了自造轮子的路途。

Android 官网的 training 教程里面，[Notepad Exercise](http://developer.android.com/training/notepad/index.html) 正好差不多是教如何开发一个类似 todo list 的 app，可以添加 / 删除 notes，可以修改 title 和 content。虽然各种简陋，但至少我觉得一个 todo 类的 app 的最核心功能：添加 / 修改 / 删除 notes 的功能都有了。所以就准备拿这个练手了。

Exercise 1 没有什么特别炫酷的 feature，只提供了添加 notes 的功能。主要基本介绍了 `ListActivity` 和如何使用 SQLite 来存储 notes。我不大想把教程翻译一遍，这个没什么意思，不过还是尽量把每个涉及到的函数或类按照自己的理解来解释一下。

### Class: NotesDbAdapter

这个类里面首先有这么几个变量：`KEY_TITLE`，`KEY_BODY` 和 `KEY_ROWID`。顾名思义这三个变量就是用来操作数据库用的。