# #1229 - Question about concurrent reads/writes. [Closed]

> Username: aspartamed  
> Created at: 2018-08-16 14:55:22 UTC  
> Updated at: 2018-09-23 01:59:12 UTC  
> Closed at: 2018-09-23 01:59:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1229  

I am trying to understand the boundaries of thread safety. I had some trouble working with the async calls trying to create a continuous read and write. What I have done is create 3 threads one that manages reads and one that manages writes and on that is the business logic  
  
I am using the synchronous calls to read and write. So is it thread safe to concurrently call a synchronous read at the same time as a write? what about close?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-17 00:54:16 UTC  
> Updated at: 2018-08-17 00:54:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1229#issuecomment-413726273  

None of the algorithms in Beast are thread-safe (i.e. safe to call concurrently). However, `websocket::stream` does allow one of each operation (read, write, ping/pong, close) to be active at the same time:  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/using_websocket/notes.html

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-09-16 01:38:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1229#issuecomment-421668883  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-09-23 01:59:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1229#issuecomment-423785944  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
