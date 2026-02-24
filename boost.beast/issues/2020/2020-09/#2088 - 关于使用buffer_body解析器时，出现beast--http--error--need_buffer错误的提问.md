# #2088 - 关于使用buffer_body解析器时，出现beast::http::error::need_buffer错误的提问 [Closed]

> Username: Yinpinghua  
> Created at: 2020-09-15 09:31:18 UTC  
> Updated at: 2020-11-16 03:45:54 UTC  
> Closed at: 2020-11-16 03:45:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2088  

麻烦问下，我在使用buffer_body解析器时,先读头部，然后循环读取body,会提示beast::http::error::need_buffer的错误，但我的代码能够实现功能

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-09-15 09:42:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2088#issuecomment-692601058  

对不起，我听不懂。  
  
---------------------------  
  
Sorry I don't understand.

---

## Comment 2

> Username: Yinpinghua  
> Created at: 2020-09-16 01:29:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2088#issuecomment-693115820  

我的意思，beast::http::error::need_buffer 这个错误，是不是不用管的？

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-09-16 06:41:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2088#issuecomment-693207004  

If you have no more buffer to offer,  then beast::http::error::need_buffer is an error. If you are able to respond to it and provide more buffer, it is a signal.  
  
-----------------------------------------------------------------------------------------------  
  
如果没有更多缓冲区可提供，那么beast :: http :: error :: need_buffer就是一个错误。 如果您能够响应它并提供更多的缓冲区，那是一个信号。

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-11-01 07:41:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2088#issuecomment-720048932  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2020-11-16 03:45:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2088#issuecomment-727717728  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
