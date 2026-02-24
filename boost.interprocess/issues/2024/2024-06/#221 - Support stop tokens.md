# #221 - Support stop tokens [Open]

> Username: s13n  
> Created at: 2024-06-11 12:19:17 UTC  
> Updated at: 2024-08-05 21:33:51 UTC  
> Url: https://github.com/boostorg/interprocess/issues/221  

Since C++20 with std:jthread, there is support for stop tokens in condition variables. It would be nice if the interprocess condition variables would support them, too!

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-08-05 21:33:50 UTC  
> Url: https://github.com/boostorg/interprocess/issues/221#issuecomment-2269958204  

Sadly Interprocess condition variables are optimimally wrappers around OS primitives (like POSIX condition variables), so stop_token support can't be added (e..g std::condition_variable does not support it). But maybe we can analyze if boost::interprocess::condition_variable_any can support it just like std::condition_variable_any.
