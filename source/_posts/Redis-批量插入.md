layout: post
title: "Redis 批量插入"
date: 2015-07-19 18:43:43
categories: [redis]
tags: [redis]
keywords: redis, pipe, 批量插入
description: redis批量插入
comments: true
photos:
-
---
有时候，一些redis用例需要在短时间内插入大量已有或由用户临时生成的数据，导致百万级的键-值对需要在尽可能短的时间内创建。

这就称作*批量插入*，这篇文档的目的就是探讨如何尽可能快的向redis插入数据。

### ** *使用protocol* **

因为某些原因，通过一个普通的redis客户端来实现数据的批量插入并不可取，比如：一个很小白的方式就是一个命令接一个命令的发送，这样会很慢，因为每个命令都会消耗一个回路时间（因为redis是一个client-server模式的应用，客户端和服务器可能隔了十万八千里）。这种情况也许可以通过使用管道方式（[pipeling](http://redis.io/topics/pipelining)）来解决，但是对于大量数据的批量插入，往往需要读取返回值的同时创建新命令，还要确保尽可能快的插入。
<!--more-->
只有少量的客户端支持非阻塞I/O（貌似是说大多数redis客户端是单线程的），并且并不是所有的客户端都有能力以一种有效的方式解析返回数据以实现吞吐量的最大化。由于上面的一些原因，向redis批量插入数据的首选方式便是生成一个包含redis协议的文件，以RAW（一个没有被NT文件系统（FAT或NTFS）格式化的磁盘分区）形式格式化，以便调用相关的命令插入请求数据。

举例来说，我需要生成大量的set格式数据，其中包含数以亿计的'keyN->valueN'形式的键值，我会创建一个基于Redis协议格式的包含如下命令的文件：

    SET Key0 Value0
    SET Key1 Value1
    ...
    SET KeyN ValueN

*注意：
　　用shell组成上面格式的数据后，用redis-cli --pipe方式导入，报如下错误
　　All data transferred. Waiting for the last reply...
　　ERR syntax error
　　Last reply received from server.
　　errors: 1, replies: 1
　　经调查是因为linux文档的换行是\n,但文档要求每行的结尾是\r\n.
　　最后用unix2dos命令将文件转换后，再执行redis-cli --pipe，不再出现错误*

一旦文件创建，接下来的事情就是尽可能快的插入到 Redis 里。过去的实现方式，是使用如下 netcat 命令：

    (cat data.txt; sleep 10) | nc localhost 6379 > /dev/null

然而这并不是一个实现数据批量插入的可靠方式，是因为 necat 无法确切的知道数据何时传输完毕，并且不能查错。在2.6或者以后的Redis版本里，客户端可以支持一种叫做管道模式（ pipe mode ）的新模式，用来处理批量插入。

可以使用类似下面的pipe model 命令：

    cat data.txt | redis-cli --pipe

会产生如下类似输出：

    All data transferred. Waiting for the last reply...
    Last reply recived from server.
    Erroe: 0, replies: 100000

拥有这种模式的redis-cli同时会确保只把从Redis服务收到的错误信息输出。

### ** *Redis protocol的生成* **
Redis协议生成和解析都是十分简单的，参考文档请点击[这里](http://redis.io/topics/protocol)。然而你并不需要理解为批量插入而生成的协议的每个细节，其中的每个命令仅以如下方式呈现：

    *<args><cr><lf>
    $<len><cr><lf>
    <args0><cr><lf>
    <args1><cr><lf>
    <args2><cr><lf>
    ...
    <argsN><cr><lf>

这里的`<cr>`代表"\r"(或者是ASII码 13)， `<lf>`代表"\n"(或者是ASII码 10)。

举个例子，**SET key value**命令可以用一下协议表示：

    *3<cr><lf>
    $3<cr><lf>
    SET<cr><lf>
    $4<cr><lf>
    name<cr><lf>
    $5<cr><lf>
    lunar<cr><lf>

*格式说明：*
　　\**3 #表示有3个参数
　　$3 #表示“参数”有三个字节
　　SET #执行的命令
　　$34 # key有 4个字节
　　name #key对应的值
　　$5 #field对应的长度
　　lunar #field对应的值
　　每行默认以 \r\n 结尾
　　同时在执行完一行后，以 \r\n 代码一条语句结束*

或者用一串字符串来表示：

    "*3\r\n$3\r\nSET\r\n$3\r\nkey\r\n$5\r\nvalue\r\n"

你生成的用于批量插入的文件，就是由上面的命令一个接一个的构成的。

下面的Ruby函数可以用来生成一个有效的协议：

    def gen_redis_proto(*cmd)
        proto = ""
        proto << "*" + cmd.lenght.to_s + "\r\n"
        cmd.each{|arg|
            proto << "$" + arg.to_s.bytesize.to_s +"\r\n"
            proto << arg.to_s + "\r\n"
        }
        proto
    end
    puts gen_redis_proto("SET", "mykey", "Hello World!").inspect

使用上述方法我们可以很容易的生成上述键值对，函数用法如下：

    (0...1000).each{|n|
        STDOUT.write(gen_redis_proto("SET", "mykey#{n}", "Hello World#{n}"))
    }

我们可以通过redis-cli的pipe直接执行上述程序，来实现大规模导入。

    $ ruby proto.rb | redis-cli --pipe
    All data transferred. Waiting for the last reply...
    Last reply received from server.
    errors: 0, replies: 1000

### ** *Redis引擎下 pipe mode 是怎样工作的* **

redis-cli内置的 pipe mode 的神奇之处在于，能够像netcat一样快，与此同时还能捕捉到服务器返回的最后一条数据。

好处如下：

- `redis-cli --pipe` 尝试尽可能快的向服务器发送数据。
- 同时读取可用数据并解析。
- 一旦输出接口没有多余的可读数据，它就发送一个20bytes长度特殊*ECHO*命令：我们可以确定这就是最后一个命令，如果我们收到了一个同样的20bytes的返回值，我们就可以匹配到这个检测用的返回值。
- 一旦这个特殊命令发送，返回值接收代码就会匹配这个20bytes的返回值，一旦匹配成功就可以退出进程。

使用这个方法，我们就不需要解析为了统计命令数量而发送到服务器的协议，而是紧紧解析返回值。

然而，我们还是设置了一个计数器，来统计解析的返回值数量，以便告诉用户有多少条命令发送到服务器。
