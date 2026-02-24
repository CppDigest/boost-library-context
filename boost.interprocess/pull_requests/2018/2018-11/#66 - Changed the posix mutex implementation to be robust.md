# #66 Changed the posix mutex implementation to be robust [Closed]

> Username: PomLover  
> Created at: 2018-11-09 19:22:25 UTC  
> Updated at: 2022-11-14 22:38:03 UTC  
> Closed at: 2018-11-09 20:10:58 UTC  
> Url: https://github.com/boostorg/interprocess/pull/66  

Added posix robust mutex implementation to allow subsequent mutex lock can grab the lock from previous dead owner. And it also fixed the issue #65.  
  
Hi Reviewers,  
 We ran into some issues while we are using boost::interprocess::named_recursive_mutex with posix implementation when the previous owner of the mutex has been dead without releasing the mutex, so all others which are waiting for the same mutex will be blocked forever. And that's why I added this fix in posix mutex implementation to avoid this issue.

---

## Comment 1

> Username: Dalzhim  
> Created_at: 2022-11-11 19:45:08 UTC  
> Updated_at: 2022-11-11 19:45:20 UTC  
> Url: https://github.com/boostorg/interprocess/pull/66#issuecomment-1312136905  

Why was this PR closed? It seems to be still quite relevant!

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2022-11-13 22:13:25 UTC  
> Url: https://github.com/boostorg/interprocess/pull/66#issuecomment-1312835799  

Because robust mutex support was added in the commit: https://github.com/boostorg/interprocess/commit/7c8893788d3823615d6ddbfd125d2ad153817111

---

## Comment 3

> Username: Dalzhim  
> Created_at: 2022-11-14 22:38:03 UTC  
> Url: https://github.com/boostorg/interprocess/pull/66#issuecomment-1314501362  

Thank you!

---
