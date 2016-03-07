layout: post
title: "小技巧-MySQL时间字段设置默认及自动更新"
date: 2015-07-21 10:12:36
categories: [mysql]
tags: [mysql]
keywords: mysql, timstamp, 默认时间, 自动更新
description: mysql默认时间设置
comments: true
photos:
-
---
设置默认创建时间

    `create_time` timstamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间'

设置默认更新时间并在修改时自动更新

    `update_time` timstamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '修改日期'
