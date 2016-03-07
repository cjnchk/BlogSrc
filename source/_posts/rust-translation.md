layout: post
title: "rust translation"
date: 2015-11-20 15:27:03
categories: [rust]
tags:
keywords:
description:
comments: true
photos:
-
---
Mutability, 改变某个事物的能力，在Rust中的表现与其他语言有所不同。第一个方面就是没有默认状态：

```
let x = 5;
x = 6;  // error!
```
我们可以通过`mut`关键字来声明：

```
let mut x = 5;
x = 6; // no problem!
```
这是一个可变的变量绑定。当绑定是可变的时候，意味着你可以改变此绑定。所以在上面的例子中，`x`可以从一个值变为另一个值。

如果你想......


## Interior vs. Exterior Mutability

在Rust里说某事物是'immutable'，并不意味着是不可变的：
