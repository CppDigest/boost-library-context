# #45 - Docs must explain the async_run rationale [Closed]

> Username: vinniefalco  
> Created at: 2023-01-19 15:36:20 UTC  
> Updated at: 2023-03-02 06:42:08 UTC  
> Closed at: 2023-03-02 06:42:08 UTC  
> Url: https://github.com/boostorg/redis/issues/45  

The documentation should explain the rationale for this:  
```  
co_await ( conn->async_run() || conn->async_exec( req, adapt(resp) ) );  
```  
  
A "FAQ" section could be a good approach.

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-02-26 13:15:13 UTC  
> Url: https://github.com/boostorg/redis/issues/45#issuecomment-1445359510  

I am removing the emphasis on operator|| from the main doc. There will be only a brief mention that for certain cases this might be useful, here are the changes: https://github.com/boostorg/redis/tree/51-more-suggestions-for-documentation-improvements.

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-02-26 19:59:40 UTC  
> Url: https://github.com/boostorg/redis/issues/45#issuecomment-1445452861  

Fix: https://github.com/boostorg/redis/commit/d01a9acf3b5b8f6a942cc8339f2308e9b85889e5
