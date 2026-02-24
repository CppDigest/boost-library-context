# #1657 - How to detect http close using async ssl? [Closed]

> Username: smartseal  
> Created at: 2019-07-16 02:04:21 UTC  
> Updated at: 2019-08-26 08:18:42 UTC  
> Closed at: 2019-08-26 08:18:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1657  

I use HTTP REST and server will close the connection each time after sending response.  
  
I use async_read. I didn't shutdown the connect because it will report "short read" error and the server will shutdown the connection.  
  
I want to get an callback when the connection is shutdown, how to detect it?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-07-16 13:33:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1657#issuecomment-511817881  

Is your program a client or a server?

---

## Comment 2

> Username: smartseal  
> Created at: 2019-07-20 08:08:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1657#issuecomment-513447220  

Client program.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-08-19 08:12:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1657#issuecomment-522461855  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-08-26 08:18:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1657#issuecomment-524767065  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
