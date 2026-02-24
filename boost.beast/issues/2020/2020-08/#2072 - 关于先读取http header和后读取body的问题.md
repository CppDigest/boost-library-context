# #2072 - 关于先读取http header和后读取body的问题 [Closed]

> Username: Yph0258691  
> Created at: 2020-08-27 23:47:39 UTC  
> Updated at: 2020-10-12 00:33:08 UTC  
> Closed at: 2020-10-12 00:33:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2072  

麻烦问下，我这边http::async_read_header调用这个函数之后，我想读取body，我该调用哪个函数？

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-28 06:12:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-682346138  

https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read/overload1.html  
  
确保使用相同的解析器和动态缓冲区

---

## Comment 2

> Username: Yph0258691  
> Created at: 2020-08-28 07:56:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-682385719  

1：解析器和动态缓冲区，字节是否要偏移？

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-08-28 08:22:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-682396549  

解析器和动态缓冲区一起包含读取的当前状态以及尚未解析的任何其他数据。 到目前为止，它们一起包含读取操作的完整状态。  
  
调用async_read_header时，您要求在解析标头后暂停读取。 随后调用async_read允许读取完成。

---

## Comment 4

> Username: Yph0258691  
> Created at: 2020-08-28 08:36:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-682402562  

好好，我试试，谢谢您

---

## Comment 5

> Username: Yph0258691  
> Created at: 2020-08-28 08:38:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-682403553  

麻烦再问下，暂停读取，我该调用哪个函数哦？

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-08-28 08:41:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-682404897  

无需暂停。 因为数据读取将仅在对async_read_xxx的调用期间继续进行。  
  
如果您的意思是“如何关闭连接？” 然后在基础套接字上调用shutdown（）并读取直到EOF。

---

## Comment 7

> Username: Yph0258691  
> Created at: 2020-08-28 08:44:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-682406023  

那我如果调用async_read,会阻塞，因为读去服务器的数据会很大哦

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-08-28 08:45:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-682406512  

您是说要申请超时吗？  
  
如果是这样，您有两种选择：  
  
1.并行运行一个计时器-当计时器超时时，取消套接字上的异步操作（您将需要处理交叉情况）  
  
2.使用内置了超时功能的basic_ssl_stream类。  
  
在任何情况下，该程序都不会“阻塞”，因为这些操作是异步的。

---

## Comment 9

> Username: Yph0258691  
> Created at: 2020-08-28 08:48:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-682407999  

我不想设置超时，有没有办法调用async_read函数时，可以指定 buffer长度的，让底层指定读取长度

---

## Comment 10

> Username: madmongo1  
> Created at: 2020-08-28 08:51:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-682409067  

有两种选择。  
  
您可以设置body_limit（）：  
https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_parser/body_limit.html  
  
或者，您可以为DynamicBuffer指定固定长度的缓冲区：  
例如：https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/ref/boost__beast__flat_static_buffer.html

---

## Comment 11

> Username: Yph0258691  
> Created at: 2020-08-28 08:52:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-682409775  

好，我试试啊，谢谢您

---

## Comment 12

> Username: Yph0258691  
> Created at: 2020-08-28 09:08:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-682418233  

buffer_.prepare(500 * 1024 * 1024); 设置这个函数之后，最大才65536

---

## Comment 13

> Username: madmongo1  
> Created at: 2020-08-28 09:50:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-682436297  

如果套接字缓冲区中有足够的数据，我们将在缓冲区中提供足够的空间以读取65536字节的块中的请求或响应。 这样可以最大限度地减少对底层套接字的读取次数，以保持IO效率。  
此功能不限制消息的整体大小，仅限制将增量数据传递到解析器。

---

## Comment 14

> Username: Yph0258691  
> Created at: 2020-08-29 02:33:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-683223419  

嗯，懂了，谢谢啊

---

## Comment 15

> Username: stale[bot]  
> Created at: 2020-10-04 03:01:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-703193589  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 16

> Username: stale[bot]  
> Created at: 2020-10-12 00:33:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2072#issuecomment-706795765  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
