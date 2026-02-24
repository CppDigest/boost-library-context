# #1159 - Http server support browser folder [Closed]

> Username: snahmad  
> Created at: 2018-06-13 12:10:15 UTC  
> Updated at: 2018-07-20 15:41:53 UTC  
> Closed at: 2018-07-20 15:41:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1159  

Hi,  
  
Does beast support browser folder?  
  
I tried few HTTP server examples. I cannot browser my root folder?  
  
  
Thanks,  
Naeem

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-13 13:49:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1159#issuecomment-396943793  

> Does beast support browser folder?  
  
No. The examples are intended to demonstrate the usage of Beast interfaces. Iterating a directory (Boost.Filesystem or std:: filesystem) and rendering the contents in HTML has nothing to do with HTTP or WebSocket, and is out of scope for Beast.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-07-13 14:46:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1159#issuecomment-404855109  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-07-20 15:41:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1159#issuecomment-406639718  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
