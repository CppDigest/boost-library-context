# #414 - Coroutines example HTTP server (connection_pool) sometimes crashes on termination [Closed]

> Username: anarthal  
> Created at: 2025-02-11 18:18:23 UTC  
> Updated at: 2025-02-14 20:13:03 UTC  
> Closed at: 2025-02-14 20:13:03 UTC  
> Url: https://github.com/boostorg/mysql/issues/414  



---

## Comment 1

> Username: anarthal  
> Created at: 2025-02-14 15:10:53 UTC  
> Url: https://github.com/boostorg/mysql/issues/414#issuecomment-2659583683  

This seems to be an interaction between threading and Boost.Context. I've tracked the crash to https://github.com/boostorg/context/blob/cef5372547021b5d4764fa4a23b426cf0fa2504f/include/boost/context/fiber_fcontext.hpp#L94. I will make the server single-threaded for now.
