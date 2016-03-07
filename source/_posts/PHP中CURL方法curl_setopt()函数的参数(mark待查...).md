layout: post
title: "PHP中CURL方法curl_setopt()函数的参数(mark待查...)"
date: 2015-08-04 23:07:45
categories: [php]
tags: [curl, php]
keywords: curl,php
description: curl宝典
comments: true
photos:
-
---


PHP CURL curl_setopt 参数

bool curl_setopt (int ch, string option, mixed value)
curl_setopt()函数将为一个CURL会话设置选项。option参数是你想要的设置，value是这个选项给定的值。


下列选项的值将被作为长整形使用(在option参数中指定)：
?    CURLOPT_INFILESIZE : 当你上传一个文件到远程站点，这个选项告诉PHP你上传文件的大小。
?    CURLOPT_VERBOSE : 如果你想CURL报告每一件意外的事情，设置这个选项为一个非零值。
?    CURLOPT_HEADER : 如果你想把一个头包含在输出中，设置这个选项为一个非零值。
?    CURLOPT_NOPROGRESS: 如果你不会PHP为CURL传输显示一个进程条，设置这个选项为一个非零值。注意：PHP自动设置这个选项为非零值，你应该仅仅为了调试的目的来改变这个选项。
?    CURLOPT_NOBODY : 如果你不想在输出中包含body部分，设置这个选项为一个非零值。
?    CURLOPT_FAILONERROR : 如果你想让PHP在发生错误(HTTP代码返回大于等于300)时，不显示，设置这个选项为一人非零值。默认行为是返回一个正常页，忽略代码。
?    CURLOPT_UPLOAD: 如果你想让PHP为上传做准备，设置这个选项为一个非零值。
?    CURLOPT_POST : 如果你想PHP去做一个正规的HTTP POST，设置这个选项为一个非零值。这个POST是普通的 application/x-www-from-urlencoded 类型，多数被HTML表单使用。
<!--more-->
?    CURLOPT_FTPLISTONLY : 设置这个选项为非零值，PHP将列出FTP的目录名列表。
?    CURLOPT_FTPAPPEND : 设置这个选项为一个非零值，PHP将应用远程文件代替覆盖它。
?    CURLOPT_NETRC : 设置这个选项为一个非零值，PHP将在你的 ~./netrc 文件中查找你要建立连接的远程站点的用户名及密码。
?    CURLOPT_FOLLOWLOCATION : 设置这个选项为一个非零值(象 “Location: “)的头，服务器会把它当做HTTP头的一部分发送(注意这是递归的，PHP将发送形如 “Location: “的头)。
?    CURLOPT_PUT : 设置这个选项为一个非零值去用HTTP上传一个文件。要上传这个文件必须设置CURLOPT_INFILE和CURLOPT_INFILESIZE选项.
?    CURLOPT_MUTE : 设置这个选项为一个非零值，PHP对于CURL函数将完全沉默。
?    CURLOPT_TIMEOUT : 设置一个长整形数，作为最大延续多少秒。
?    CURLOPT_LOW_SPEED_LIMIT: 设置一个长整形数，控制传送多少字节。
?    CURLOPT_LOW_SPEED_TIME : 设置一个长整形数，控制多少秒传送CURLOPT_LOW_SPEED_LIMIT规定的字节数。
?    CURLOPT_RESUME_FROM : 传递一个包含字节偏移地址的长整形参数，(你想转移到的开始表单)。
?    CURLOPT_SSLVERSION: 传递一个包含SSL版本的长参数。默认PHP将被它自己努力的确定，在更多的安全中你必须手工设置。
?    CURLOPT_TIMECONDITION : 传递一个长参数，指定怎么处理CURLOPT_TIMEVALUE参数。你可以设置这个参数为TIMECOND_IFMODSINCE 或 TIMECOND_ISUNMODSINCE。这仅用于HTTP。
?    CURLOPT_TIMEVALUE : 传递一个从1970-1-1开始到现在的秒数。这个时间将被CURLOPT_TIMEVALUE选项作为指定值使用，或被默认TIMECOND_IFMODSINCE使用。

下列选项的值将被作为字符串：
?    CURLOPT_URL: 这是你想用PHP取回的URL地址。你也可以在用curl_init()函数初始化时设置这个选项。
?    CURLOPT_USERPWD : 传递一个形如[username]:[password]风格的字符串,作用PHP去连接。
?    CURLOPT_PROXYUSERPWD : 传递一个形如[username]:[password] 格式的字符串去连接HTTP代理。
?    CURLOPT_RANGE : 传递一个你想指定的范围。它应该是”X-Y”格式，X或Y是被除外的。HTTP传送同样支持几个间隔，用逗句来分隔(X-Y,N-M)。
?    CURLOPT_POSTFIELDS : 传递一个作为HTTP “POST”操作的所有数据的字符串。
?    CURLOPT_REFERER: 在HTTP请求中包含一个”referer”头的字符串。
?    CURLOPT_USERAGENT : 在HTTP请求中包含一个”user-agent”头的字符串。
?    CURLOPT_FTPPORT: 传递一个包含被ftp “POST”指令使用的IP地址。这个POST指令告诉远程服务器去连接我们指定的IP地址。这个字符串可以是一个IP地址，一个主机名，一个网络界面名(在UNIX下)，或是‘-’(使用系统默认IP地址)。
?    CURLOPT_COOKIE : 传递一个包含HTTP cookie的头连接。
?    CURLOPT_SSLCERT : 传递一个包含PEM格式证书的字符串。
?    CURLOPT_SSLCERTPASSWD : 传递一个包含使用CURLOPT_SSLCERT证书必需的密码。
?    CURLOPT_COOKIEFILE : 传递一个包含cookie数据的文件的名字的字符串。这个cookie文件可以是Netscape格式，或是堆存在文件中的HTTP风格的头。
?    CURLOPT_CUSTOMREQUEST : 当进行HTTP请求时，传递一个字符被GET或HEAD使用。为进行DELETE或其它操作是有益的，更Pass a string to be used instead of GET or HEAD when doing an HTTP request. This is useful for doing or another, more obscure, HTTP request. 注意: 在确认你的服务器支持命令先不要去这样做。下列的选项要求一个文件描述(通过使用fopen()函数获得)：
?    CURLOPT_FILE: 这个文件将是你放置传送的输出文件，默认是STDOUT.
?    CURLOPT_INFILE : 这个文件是你传送过来的输入文件。
?    CURLOPT_WRITEHEADER : 这个文件写有你输出的头部分。
?    CURLOPT_STDERR : 这个文件写有错误而不是stderr。用来获取需要登录的页面的例子,当前做法是每次或许都登录一次,有需要的人再做改进了.



例一：

```
$cookie_jar = tempnam('./tmp','cookie');
$ch = curl_init(); curl_setopt($ch,CURLOPT_URL, 'http://******');
curl_setopt($ch, CURLOPT_POST, 1);
$request = 'email_address=&password=&action=';
curl_setopt($ch, CURLOPT_POSTFIELDS, $request); //把返回来的cookie信息保存在$cookie_jar文件中
curl_setopt($ch, CURLOPT_COOKIEJAR, $cookie_jar); //设定返回的数据是否自动显示
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1); //设定是否显示头信息
curl_setopt($ch, CURLOPT_HEADER, false); //设定是否输出页面内容
curl_setopt($ch, CURLOPT_NOBODY, false);
curl_exec($ch);
curl_close($ch); //get data after login
```


例二：

```
$ch2 = curl_init();
curl_setopt($ch2, CURLOPT_URL, 'http://*****');
curl_setopt($ch2, CURLOPT_HEADER, false);
curl_setopt($ch2, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch2, CURLOPT_COOKIEFILE, $cookie_jar);
$orders = curl_exec($ch2);
echo '';

echo strip_tags($orders);
echo '';
curl_close($ch2); 实践证明很稳定:)
```


例三：

```
set_time_limit(0);
function _rand() {
$length=26;
$chars = "0123456789abcdefghijklmnopqrstuvwxyz";
$max = strlen($chars) - 1;
mt_srand((double)microtime() * 1000000);
$string = '';
for($i = 0; $i < $length; $i++) {
$string .= $chars[mt_rand(0, $max)];
}
return $string;
}
$HTTP_SESSION=_rand();
echo $HTTP_SESSION;
$HTTP_Server="www.baidu.com";
$HTTP_URL="/";
$ch = curl_init();
curl_setopt ($ch,CURLOPT_URL,"http://".$HTTP_Server.$HTTP_URL);
curl_setopt($ch,CURLOPT_RETURNTRANSFER,true);
curl_setopt($ch,CURLOPT_USERAGENT,"Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR 1.1.4322; .NET CLR 2.0.50727)");
//curl_setopt($ch,CURLOPT_COOKIE,$HTTP_SESSION);
$res = curl_exec($ch);
curl_close ($ch);
print_r($res);
```



刷论坛代码：
1、抓cookies程序：

```
$URL="http://www.yoururl.com/bbs/login.asp?action=chk";
//填入论坛的登陆页面地址
$ch = curl_init();
curl_setopt($ch,CURLOPT_URL,$URL);
curl_setopt($ch,CURLOPT_REFERER,"http://www.hxfoods.com/bbs/login.asp");
//设置，访问页面的来源地址

curl_setopt($ch,CURLOPT_POST,1);
curl_setopt($ch,CURLOPT_POSTFIELDS,'username=→→敢死队→&password=168168');
//分析登陆页面，把用户名，密码分别对应起来
curl_setopt ($ch, CURLOPT_HEADER,true);
//使能显示http头，
curl_exec($ch);
if (curl_errno($ch))
{
print curl_error($ch);
}
else
{
curl_close($ch);
}
```


2、刷楼了：

```
set_time_limit(0);
//设置程序执行时间无限制
$i=10000;
//耍10000次
for($j=0;$j<$i;$j++)
{
$URL="http://www.yoururl.com/bbs/savepost.asp";
//这个地址是回复表单里面action的url地址
$ch = curl_init();
curl_setopt($ch,CURLOPT_URL,$URL);
curl_setopt($ch,CURLOPT_REFERER,"http://www.hxfoods.com/bbs/dispbbs.asp?boardid=14&replyid=672709&id=127437&page=1&skin=0&Star=53");
//设置来源地址，如果不设置，论坛服务器有可能有验证不允许回复
curl_setopt($ch,CURLOPT_COOKIESESSION,true);
//能保存cookie
curl_setopt($ch,CURLOPT_COOKIE,"DvForum=userid=24122&usercookies=0&userhidden=2&password=w0reu3g775VrY745&userclass=%96%7C&username=%A1%FA%A1%FA%B8%D2%CB%C0%B6%D3%A1%FA&StatUserID=2194783945 ");
//这儿就是设置cookie了
curl_setopt($ch,CURLOPT_POST,1);
curl_setopt($ch,CURLOPT_NOBODY,1);
//不显示内容，因为有很多论坛回复后要自动跳转。
curl_setopt($ch,CURLOPT_POSTFIELDS,'Body=gfdfgdfgasdfgdfgdfgdfg& followup=672709&RootID=127437&star=58&TotalUseTable=Dv_bbs3& amp;UserName=→→敢死队→&signflag=1&total=65535');
//把你分析的回复表单的参数分别赋值
curl_setopt ($ch, CURLOPT_HEADER,true);
curl_exec($ch);
if (curl_errno($ch))
{
print curl_error($ch);
}
else
{
curl_close($ch);
}
}
```


CURL相关函数

curl_close — 关闭一个curl会话
curl_copy_handle — 拷贝一个curl连接资源的所有内容和参数
curl_errno — 返回一个包含当前会话错误信息的数字编号
curl_error — 返回一个包含当前会话错误信息的字符串
curl_exec — 执行一个curl会话
curl_getinfo — 获取一个curl连接资源句柄的信息
curl_init — 初始化一个curl会话
curl_multi_add_handle — 向curl批处理会话中添加单独的curl句柄资源
curl_multi_close — 关闭一个批处理句柄资源
curl_multi_exec — 解析一个curl批处理句柄
curl_multi_getcontent — 返回获取的输出的文本流
curl_multi_info_read — 获取当前解析的curl的相关传输信息
curl_multi_init — 初始化一个curl批处理句柄资源
curl_multi_remove_handle — 移除curl批处理句柄资源中的某个句柄资源
curl_multi_select — Get all the sockets associated with the cURL extension, which can then be "selected"
curl_setopt_array — 以数组的形式为一个curl设置会话参数
curl_setopt — 为一个curl设置会话参数
curl_version — 获取curl相关的版本信息


curl_init()函数的作用初始化一个curl会话，curl_init()函数唯一的一个参数是可选的，表示一个url地址。
curl_exec()函数的作用是执行一个curl会话，唯一的参数是curl_init()函数返回的句柄。
curl_close()函数的作用是关闭一个curl会话，唯一的参数是curl_init()函数返回的句柄。

$ch = curl_init("http://www.baidu.com/");
curl_exec($ch);
curl_close($ch);


curl_version()函数的作用是获取curl相关的版本信息，curl_version()函数有一个参数，不清楚是做什么的

print_r(curl_version())



curl_getinfo()函数的作用是获取一个curl连接资源句柄的信息，curl_getinfo()函数有两个参数，第一个参数是curl的资源句柄，第二个参数是下面一些常量：

$ch = curl_init("http://www.baidu.com/");
print_r(curl_getinfo($ch));


可选的常量包括：
CURLINFO_EFFECTIVE_URL
最后一个有效的url地址
CURLINFO_HTTP_CODE
最后一个收到的HTTP代码
CURLINFO_FILETIME
远程获取文档的时间，如果无法获取，则返回值为“-1”
CURLINFO_TOTAL_TIME
最后一次传输所消耗的时间
CURLINFO_NAMELOOKUP_TIME
名称解析所消耗的时间
CURLINFO_CONNECT_TIME
建立连接所消耗的时间
CURLINFO_PRETRANSFER_TIME
从建立连接到准备传输所使用的时间
CURLINFO_STARTTRANSFER_TIME
从建立连接到传输开始所使用的时间
CURLINFO_REDIRECT_TIME
在事务传输开始前重定向所使用的时间
CURLINFO_SIZE_UPLOAD
上传数据量的总值
CURLINFO_SIZE_DOWNLOAD
下载数据量的总值
CURLINFO_SPEED_DOWNLOAD
平均下载速度
CURLINFO_SPEED_UPLOAD
平均上传速度
CURLINFO_HEADER_SIZE
header部分的大小
CURLINFO_HEADER_OUT
发送请求的字符串
CURLINFO_REQUEST_SIZE
在HTTP请求中有问题的请求的大小
CURLINFO_SSL_VERIFYRESULT
Result of SSL certification verification requested by setting CURLOPT_SSL_VERIFYPEER
CURLINFO_CONTENT_LENGTH_DOWNLOAD
从Content-Length: field中读取的下载内容长度
CURLINFO_CONTENT_LENGTH_UPLOAD
上传内容大小的说明
CURLINFO_CONTENT_TYPE
下载内容的“Content-type”值，NULL表示服务器没有发送有效的“Content-Type: header”
curl_setopt()函数的作用是为一个curl设置会话参数。curl_setopt_array()函数的作用是以数组的形式为一个curl设置会话参数。

```
$ch = curl_init();
$fp = fopen("example_homepage.txt", "w");
curl_setopt($ch, CURLOPT_FILE, $fp);
$options = array(
CURLOPT_URL => 'http://www.baidu.com/',
CURLOPT_HEADER => false
);
curl_setopt_array($ch, $options);
curl_exec($ch);
curl_close($ch);
fclose($fp);
```



可设置的参数有：
CURLOPT_AUTOREFERER
自动设置header中的referer信息
CURLOPT_BINARYTRANSFER
在启用CURLOPT_RETURNTRANSFER时候将获取数据返回
CURLOPT_COOKIESESSION
启用时curl会仅仅传递一个session cookie，忽略其他的cookie，默认状况下curl会将所有的cookie返回给服务端。session cookie是指那些用来判断服务器端的session是否有效而存在的cookie。
CURLOPT_CRLF
启用时将Unix的换行符转换成回车换行符。
CURLOPT_DNS_USE_GLOBAL_CACHE
启用时会启用一个全局的DNS缓存，此项为线程安全的，并且默认为true。
CURLOPT_FAILONERROR
显示HTTP状态码，默认行为是忽略编号小于等于400的HTTP信息
CURLOPT_FILETIME
启用时会尝试修改远程文档中的信息。结果信息会通过curl_getinfo()函数的CURLINFO_FILETIME选项返回。
CURLOPT_FOLLOWLOCATION
启用时会将服务器服务器返回的“Location:”放在header中递归的返回给服务器，使用CURLOPT_MAXREDIRS可以限定递归返回的数量。
CURLOPT_FORBID_REUSE
在完成交互以后强迫断开连接，不能重用。
CURLOPT_FRESH_CONNECT
强制获取一个新的连接，替代缓存中的连接。
CURLOPT_FTP_USE_EPRT
TRUE to use EPRT (and LPRT) when doing active FTP downloads. Use FALSE to disable EPRT and LPRT and use PORT only.
Added in PHP 5.0.0.
CURLOPT_FTP_USE_EPSV
TRUE to first try an EPSV command for FTP transfers before reverting back to PASV. Set to FALSE to disable EPSV.
CURLOPT_FTPAPPEND
TRUE to append to the remote file instead of overwriting it.
CURLOPT_FTPASCII
An alias of CURLOPT_TRANSFERTEXT. Use that instead.
CURLOPT_FTPLISTONLY
TRUE to only list the names of an FTP directory.
CURLOPT_HEADER
启用时会将头文件的信息作为数据流输出。
CURLOPT_HTTPGET
启用时会设置HTTP的method为GET，因为GET是默认是，所以只在被修改的情况下使用。
CURLOPT_HTTPPROXYTUNNEL
启用时会通过HTTP代理来传输。
CURLOPT_MUTE
讲curl函数中所有修改过的参数恢复默认值。
CURLOPT_NETRC
在连接建立以后，访问~/.netrc文件获取用户名和密码信息连接远程站点。
CURLOPT_NOBODY
启用时将不对HTML中的body部分进行输出。
CURLOPT_NOPROGRESS
启用时关闭curl传输的进度条，此项的默认设置为true
CURLOPT_NOSIGNAL
启用时忽略所有的curl传递给php进行的信号。在SAPI多线程传输时此项被默认打开。
CURLOPT_POST
启用时会发送一个常规的POST请求，类型为：application/x-www-form-urlencoded，就像表单提交的一样。
CURLOPT_PUT
启用时允许HTTP发送文件，必须同时设置CURLOPT_INFILE和CURLOPT_INFILESIZE
CURLOPT_RETURNTRANSFER
讲curl_exec()获取的信息以文件流的形式返回，而不是直接输出。
CURLOPT_SSL_VERIFYPEER
FALSE to stop cURL from verifying the peer's certificate. Alternate certificates to verify against can be specified with the CURLOPT_CAINFO option or a certificate directory can be specified with the CURLOPT_CAPATH option. CURLOPT_SSL_VERIFYHOST may also need to be TRUE or FALSE if CURLOPT_SSL_VERIFYPEER is disabled (it defaults to 2). TRUE by default as of cURL 7.10. Default bundle installed as of cURL 7.10.
CURLOPT_TRANSFERTEXT
TRUE to use ASCII mode for FTP transfers. For LDAP, it retrieves data in plain text instead of HTML. On Windows systems, it will not set STDOUT to binary mode.
CURLOPT_UNRESTRICTED_AUTH
在使用CURLOPT_FOLLOWLOCATION产生的header中的多个locations中持续追加用户名和密码信息，即使域名已发生改变。
CURLOPT_UPLOAD
启用时允许文件传输
CURLOPT_VERBOSE
启用时会汇报所有的信息，存放在STDERR或指定的CURLOPT_STDERR中
CURLOPT_BUFFERSIZE
每次获取的数据中读入缓存的大小，这个值每次都会被填满。
CURLOPT_CLOSEPOLICY
不是CURLCLOSEPOLICY_LEAST_RECENTLY_USED就是CURLCLOSEPOLICY_OLDEST，还存在另外三个，但是curl暂时还不支持。.
CURLOPT_CONNECTTIMEOUT
在发起连接前等待的时间，如果设置为0，则不等待。
CURLOPT_DNS_CACHE_TIMEOUT
设置在内存中保存DNS信息的时间，默认为120秒。
CURLOPT_FTPSSLAUTH
The FTP authentication method (when is activated): CURLFTPAUTH_SSL (try SSL first), CURLFTPAUTH_TLS (try TLS first), or CURLFTPAUTH_DEFAULT (let cURL decide).
CURLOPT_HTTP_VERSION
设置curl使用的HTTP协议，CURL_HTTP_VERSION_NONE（让curl自己判断），CURL_HTTP_VERSION_1_0（HTTP/1.0），CURL_HTTP_VERSION_1_1（HTTP/1.1）
CURLOPT_HTTPAUTH
使用的HTTP验证方法，可选的值 有：CURLAUTH_BASIC，CURLAUTH_DIGEST，CURLAUTH_GSSNEGOTIATE，CURLAUTH_NTLM，CURLAUTH_ANY，CURLAUTH_ANYSAFE， 可以使用“|”操作符分隔多个值，curl让服务器选择一个支持最好的值，CURLAUTH_ANY等价于CURLAUTH_BASIC | CURLAUTH_DIGEST | CURLAUTH_GSSNEGOTIATE | CURLAUTH_NTLM，CURLAUTH_ANYSAFE等价于CURLAUTH_DIGEST | CURLAUTH_GSSNEGOTIATE | CURLAUTH_NTLM
CURLOPT_INFILESIZE
设定上传文件的大小
CURLOPT_LOW_SPEED_LIMIT
当传输速度小于CURLOPT_LOW_SPEED_LIMIT时，PHP会根据CURLOPT_LOW_SPEED_TIME来判断是否因太慢而取消传输。
CURLOPT_LOW_SPEED_TIME
The number of seconds the transfer should be below CURLOPT_LOW_SPEED_LIMIT for PHP to consider the transfer too slow and abort.
当传输速度小于CURLOPT_LOW_SPEED_LIMIT时，PHP会根据CURLOPT_LOW_SPEED_TIME来判断是否因太慢而取消传输。
CURLOPT_MAXCONNECTS
允许的最大连接数量，超过是会通过CURLOPT_CLOSEPOLICY决定应该停止哪些连接
CURLOPT_MAXREDIRS
指定最多的HTTP重定向的数量，这个选项是和CURLOPT_FOLLOWLOCATION一起使用的。
CURLOPT_PORT
一个可选的用来指定连接端口的量
CURLOPT_PROXYAUTH
The HTTP authentication method(s) to use for the proxy connection. Use the same bitmasks as described in CURLOPT_HTTPAUTH. For proxy authentication, only CURLAUTH_BASIC and CURLAUTH_NTLM are currently supported.
CURLOPT_PROXYPORT
The port number of the proxy to connect to. This port number can also be set in CURLOPT_PROXY.
CURLOPT_PROXYTYPE
Either CURLPROXY_HTTP (default) or CURLPROXY_SOCKS5.
CURLOPT_RESUME_FROM
在恢复传输时传递一个字节偏移量（用来断点续传）
CURLOPT_SSL_VERIFYHOST
1 to check the existence of a common name in the SSL peer certificate.
2 to check the existence of a common name and also verify that it matches the hostname provided.
CURLOPT_SSLVERSION
The SSL version (2 or 3) to use. By default PHP will try to determine this itself, although in some cases this must be set manually.
CURLOPT_TIMECONDITION
如果在CURLOPT_TIMEVALUE指定的某个时间以后被编辑过，则使用CURL_TIMECOND_IFMODSINCE返回页面，如果没有被修 改过，并且CURLOPT_HEADER为true，则返回一个"304 Not Modified"的header，CURLOPT_HEADER为false，则使用CURL_TIMECOND_ISUNMODSINCE，默认值为 CURL_TIMECOND_IFMODSINCE
CURLOPT_TIMEOUT
设置curl允许执行的最长秒数
CURLOPT_TIMEVALUE
设置一个CURLOPT_TIMECONDITION使用的时间戳，在默认状态下使用的是CURL_TIMECOND_IFMODSINCE
CURLOPT_CAINFO
The name of a file holding one or more certificates to verify the peer with. This only makes sense when used in combination with CURLOPT_SSL_VERIFYPEER.
CURLOPT_CAPATH
A directory that holds multiple CA certificates. Use this option alongside CURLOPT_SSL_VERIFYPEER.
CURLOPT_COOKIE
设定HTTP请求中“Set-Cookie:”部分的内容。
CURLOPT_COOKIEFILE
包含cookie信息的文件名称，这个cookie文件可以是Netscape格式或者HTTP风格的header信息。
CURLOPT_COOKIEJAR
连接关闭以后，存放cookie信息的文件名称
CURLOPT_CUSTOMREQUEST
A custom request method to use instead of "GET" or "HEAD" when doing a HTTP request. This is useful for doing "DELETE" or other, more obscure HTTP requests. Valid values are things like "GET", "POST", "CONNECT" and so on; i.e. Do not enter a whole HTTP request line here. For instance, entering "GET /index.html HTTP/1.0\r\n\r\n" would be incorrect.
Note: Don't do this without making sure the server supports the custom request method first.
CURLOPT_EGBSOCKET
Like CURLOPT_RANDOM_FILE, except a filename to an Entropy Gathering Daemon socket.
CURLOPT_ENCODING
header中“Accept-Encoding: ”部分的内容，支持的编码格式为："identity"，"deflate"，"gzip"。如果设置为空字符串，则表示支持所有的编码格式
CURLOPT_FTPPORT
The value which will be used to get the IP address to use for the FTP "POST" instruction. The "POST" instruction tells the remote server to connect to our specified IP address. The string may be a plain IP address, a hostname, a network interface name (under Unix), or just a plain '-' to use the systems default IP address.
CURLOPT_INTERFACE
在外部网络接口中使用的名称，可以是一个接口名，IP或者主机名。
CURLOPT_KRB4LEVEL
KRB4(Kerberos 4)安全级别的设置，可以是一下几个值之一："clear"，"safe"，"confidential"，"private"。默认的值 为"private"，设置为null的时候表示禁用KRB4，现在KRB4安全仅能在FTP传输中使用。
CURLOPT_POSTFIELDS
在HTTP中的“POST”操作。如果要传送一个文件，需要一个@开头的文件名
CURLOPT_PROXY
设置通过的HTTP代理服务器
CURLOPT_PROXYUSERPWD
连接到代理服务器的，格式为“[username]:[password]”的用户名和密码。
CURLOPT_RANDOM_FILE
设定存放SSL用到的随机数种子的文件名称
CURLOPT_RANGE
设置HTTP传输范围，可以用“X-Y”的形式设置一个传输区间，如果有多个HTTP传输，则使用逗号分隔多个值，形如："X-Y,N-M"。
CURLOPT_REFERER
设置header中"Referer: " 部分的值。
CURLOPT_SSL_CIPHER_LIST
A list of ciphers to use for SSL. For example, RC4-SHA and TLSv1 are valid cipher lists.
CURLOPT_SSLCERT
传递一个包含PEM格式证书的字符串。
CURLOPT_SSLCERTPASSWD
传递一个包含使用CURLOPT_SSLCERT证书必需的密码。
CURLOPT_SSLCERTTYPE
The format of the certificate. Supported formats are "PEM" (default), "DER", and "ENG".
CURLOPT_SSLENGINE
The identifier for the crypto engine of the private SSL key specified in CURLOPT_SSLKEY.
CURLOPT_SSLENGINE_DEFAULT
The identifier for the crypto engine used for asymmetric crypto operations.
CURLOPT_SSLKEY
The name of a file containing a private SSL key.
CURLOPT_SSLKEYPASSWD
The secret password needed to use the private SSL key specified in CURLOPT_SSLKEY.
Note: Since this option contains a sensitive password, remember to keep the PHP script it is contained within safe.
CURLOPT_SSLKEYTYPE
The key type of the private SSL key specified in CURLOPT_SSLKEY. Supported key types are "PEM" (default), "DER", and "ENG".
CURLOPT_URL
需要获取的URL地址，也可以在PHP的curl_init()函数中设置。
CURLOPT_USERAGENT
在HTTP请求中包含一个”user-agent”头的字符串。
CURLOPT_USERPWD
传递一个连接中需要的用户名和密码，格式为：“[username]:[password]”。
CURLOPT_HTTP200ALIASES
设置不再以error的形式来处理HTTP 200的响应，格式为一个数组。
CURLOPT_HTTPHEADER
设置一个header中传输内容的数组。
CURLOPT_POSTQUOTE
An array of FTP commands to execute on the server after the FTP request has been performed.
CURLOPT_QUOTE
An array of FTP commands to execute on the server prior to the FTP request.
CURLOPT_FILE
设置输出文件的位置，值是一个资源类型，默认为STDOUT (浏览器)。
CURLOPT_INFILE
在上传文件的时候需要读取的文件地址，值是一个资源类型。
CURLOPT_STDERR
设置一个错误输出地址，值是一个资源类型，取代默认的STDERR。
CURLOPT_WRITEHEADER
设置header部分内容的写入的文件地址，值是一个资源类型。
CURLOPT_HEADERFUNCTION
设置一个回调函数，这个函数有两个参数，第一个是curl的资源句柄，第二个是输出的header数据。header数据的输出必须依赖这个函数，返回已写入的数据大小。
CURLOPT_PASSWDFUNCTION
设置一个回调函数，有三个参数，第一个是curl的资源句柄，第二个是一个密码提示符，第三个参数是密码长度允许的最大值。返回密码的值。
CURLOPT_READFUNCTION
设置一个回调函数，有两个参数，第一个是curl的资源句柄，第二个是读取到的数据。数据读取必须依赖这个函数。返回读取数据的大小，比如0或者EOF。
CURLOPT_WRITEFUNCTION
设置一个回调函数，有两个参数，第一个是curl的资源句柄，第二个是写入的数据。数据写入必须依赖这个函数。返回精确的已写入数据的大小


curl_copy_handle()函数的作用是拷贝一个curl连接资源的所有内容和参数

$ch = curl_init("http://www.baidu.com/");
$another = curl_copy_handle($ch);
curl_exec($another);
curl_close($another);
curl_error()函数的作用是返回一个包含当前会话错误信息的字符串。
curl_errno()函数的作用是返回一个包含当前会话错误信息的数字编号。


curl_multi_init()函数的作用是初始化一个curl批处理句柄资源。
curl_multi_add_handle()函数的作用是向curl批处理会话中添加单独的curl句柄资源。curl_multi_add_handle()函数有两个参数，第一个参数表示一个curl批处理句柄资源，第二个参数表示一个单独的curl句柄资源。
curl_multi_exec()函数的作用是解析一个curl批处理句柄，curl_multi_exec()函数有两个参数，第一个参数表示一个批处理句柄资源，第二个参数是一个引用值的参数，表示剩余需要处理的单个的curl句柄资源数量。
curl_multi_remove_handle()函数表示移除curl批处理句柄资源中的某个句柄资源，curl_multi_remove_handle()函数有两个参数，第一个参数表示一个curl批处理句柄资源，第二个参数表示一个单独的curl句柄资源。
curl_multi_close()函数的作用是关闭一个批处理句柄资源。

```
$ch1 = curl_init();
$ch2 = curl_init();
curl_setopt($ch1, CURLOPT_URL, "http://www.baidu.com/");
curl_setopt($ch1, CURLOPT_HEADER, 0);
curl_setopt($ch2, CURLOPT_URL, "http://www.google.com/");
curl_setopt($ch2, CURLOPT_HEADER, 0);
$mh = curl_multi_init();
curl_multi_add_handle($mh,$ch1);
curl_multi_add_handle($mh,$ch2);
do {
curl_multi_exec($mh,$flag);
} while ($flag > 0);
curl_multi_remove_handle($mh,$ch1);
curl_multi_remove_handle($mh,$ch2);
curl_multi_close($mh);
```

curl_multi_getcontent()函数的作用是在设置了CURLOPT_RETURNTRANSFER的情况下，返回获取的输出的文本流。
curl_multi_info_read()函数的作用是获取当前解析的curl的相关传输信息。
curl_multi_select()
Get all the sockets associated with the cURL extension, which can then be "selected"

