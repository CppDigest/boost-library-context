# #331 - Why `fiber::mutex` yields in `try_lock` even after acquiring the lock successfully? [Open]

> Username: rodman10  
> Created at: 2025-05-06 09:48:50 UTC  
> Updated at: 2025-05-06 09:48:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/331  

Recently I'm reading the source code of `fiber::mutex`, I noticed that`fiber::mutex` will yield in `try_lock` no matter whether holding the lock or not? What's the considerations behind it, and can we just return immediately after lock? Thanks in advance.  
https://github.com/boostorg/fiber/blob/238487b543e8863c647d31a82ccfc3fa11a960d9/src/mutex.cpp#L43-L60
