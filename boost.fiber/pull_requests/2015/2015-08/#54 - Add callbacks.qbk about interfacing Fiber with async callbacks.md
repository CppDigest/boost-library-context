# #54 Add callbacks.qbk about interfacing Fiber with async callbacks. [Merged]

> Username: nat-goodspeed  
> Created at: 2015-08-24 13:33:16 UTC  
> Updated at: 2016-06-06 11:36:33 UTC  
> Merged at: 2015-08-24 15:37:39 UTC  
> Closed at: 2015-08-24 15:37:39 UTC  
> Url: https://github.com/boostorg/fiber/pull/54  

This covers both generic callbacks (adapt_callbacks.cpp,  
adapt_method_calls.cpp) and custom Asio completion tokens (yield.hpp,  
promise_completion_token.hpp, detail/yield.hpp, detail/promise_handler.hpp).  
Mark up the relevant source files to provide code snippets for callbacks.qbk.

---
