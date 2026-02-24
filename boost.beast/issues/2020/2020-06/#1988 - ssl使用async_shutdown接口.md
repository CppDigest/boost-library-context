# #1988 - ssl使用async_shutdown接口 [Closed]

> Username: Yinpinghua  
> Created at: 2020-06-16 10:22:38 UTC  
> Updated at: 2020-06-21 18:09:59 UTC  
> Closed at: 2020-06-21 18:09:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1988  

麻烦问下,在ssl下调用async_shutdown接口，是否需要调用next_layer 下的socket的shutdown()函数

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-06-16 11:35:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1988#issuecomment-644707780  

如果要关闭连接，则可以不执行SSL关闭。您可以简单地遵循以下顺序：  
1. Websocket异步关闭  
2.当所有未完成的完成处理程序都完成后，关闭（或删除）整个流。  
  
Asio会确保在底层套接字句柄上调用`close（）`之前，确保底层套接字调用函数`shutdown（）`。

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-06-21 18:09:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1988#issuecomment-647161950  

Question answered.  
Closing issue.  
You are welcome to ask further questions if you encounter difficulties.
