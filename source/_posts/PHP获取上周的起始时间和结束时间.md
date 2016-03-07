layout: post
title: "小技巧-PHP获取上周的起始时间和结束时间"
date: 2015-08-15 17:06:36
categories: [php]
tags:
keywords:
description:
comments: true
photos:
-
---
就像下面这样
```
$lastWeekStart = mktime(0,0,0,date('m'),date('d')-date('w')+1-7,date('Y'));
$lastWeekEnd = mktime(23,59,59,date('m'),date('d')-date('w')+7-7,date('Y'));
```
