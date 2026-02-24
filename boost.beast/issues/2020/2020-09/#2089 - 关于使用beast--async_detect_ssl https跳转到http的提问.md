# #2089 - 关于使用beast::async_detect_ssl https跳转到http的提问 [Closed]

> Username: Yinpinghua  
> Created at: 2020-09-16 10:09:22 UTC  
> Updated at: 2020-11-16 03:45:57 UTC  
> Closed at: 2020-11-16 03:45:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2089  

麻烦问下，我想调用此函数之后，想实现https跳转到http，会报证书不识别的错误，麻烦问下我该如何解决

---

## Comment 1

> Username: Yinpinghua  
> Created at: 2020-09-16 10:12:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2089#issuecomment-693309864  

还有当客户端使用 https请求后，调用这个函数之后，能不能把 beast::flat_buffer 转换为https格式的数据

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-09-16 10:59:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2089#issuecomment-693331747  

I am not sure I understand.  
  
You will supply a buffer when issuing async_read operations. The operations may read more data than was necessary for the current operation. In this case you would consume() the "bytes_transfered" from the buffer and re-use the buffer when servicing the next request.  
  
One HTTP session should be over either TCP or SSL/TLS. You would upgrade the connection before handling HTTP requests.  
  
----------------------------------------------------------------------------------------  
  
我不确定我是否理解。 发出async_read操作时，您将提供一个缓冲区。 这些操作可能会读取比当前操作所需更多的数据。 在这种情况下，您可以从缓冲区“ consume（）”“ bytes_transfered”，并在处理下一个请求时重新使用缓冲区。 一个HTTP会话应通过TCP或SSL / TLS。 您将在处理HTTP请求之前升级连接。

---

## Comment 3

> Username: Yinpinghua  
> Created at: 2020-09-17 01:12:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2089#issuecomment-693748245  

不对，我这边使用beast::async_detect_ssl这个函数来判断是否为http或者https,然后检测到https时，我想实现一个https跳转到http的功能

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-09-18 09:53:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2089#issuecomment-694773176  

The jump to SSL occurs before the HTTP/HTTPS transaction.  
Once the connection is SSL, to convert back to TCP you would need to go through the ssl::async_shutdown(). This leaves the TCP stream open but the SSL session is finished.  
  
----------------------------------------------------------------------------  
  
跳转到SSL发生在HTTP / HTTPS事务之前。 一旦连接为SSL，要转换回TCP，您将需要通过ssl :: async_shutdown（）。 这将使TCP流保持打开状态，但SSL会话已完成。

---

## Comment 5

> Username: Yinpinghua  
> Created at: 2020-09-21 01:31:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2089#issuecomment-695866696  

谢谢， 我再看看

---

## Comment 6

> Username: stale[bot]  
> Created at: 2020-11-01 07:41:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2089#issuecomment-720048924  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: stale[bot]  
> Created at: 2020-11-16 03:45:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2089#issuecomment-727717742  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
