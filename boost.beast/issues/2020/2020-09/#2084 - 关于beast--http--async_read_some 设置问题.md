# #2084 - 关于beast::http::async_read_some 设置问题 [Closed]

> Username: Yinpinghua  
> Created at: 2020-09-10 03:20:28 UTC  
> Updated at: 2020-11-01 07:41:56 UTC  
> Closed at: 2020-11-01 07:41:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2084  

麻烦问下：  
beast::http::async_read_some此接口，如果设置大于512个字节读取

---

## Comment 1

> Username: Yinpinghua  
> Created at: 2020-09-10 03:21:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2084#issuecomment-689952444  

底层代码看到，是默认是512个字节读取，有没有办法设置，大于512个字节读取

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-09-10 11:53:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2084#issuecomment-690217884  

内部异步操作尝试读取65536字节的块以提高效率。 因此，它将过度读取多余的字节并将其存储在动态缓冲区中，以准备下一次调用async_read_some。  
  
-----------------------------------------------------  
  
The internal asynchronous operation attempts to read in blocks of 65536 bytes for efficiency. For this reason it will over-read and store the excess bytes in the dynamic buffer ready for the next call to async_read_some.

---

## Comment 3

> Username: Yinpinghua  
> Created at: 2020-09-11 08:56:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2084#issuecomment-690966846  

好，明白了，谢谢您啊

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-10-11 23:32:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2084#issuecomment-706785639  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2020-11-01 07:41:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2084#issuecomment-720048988  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
