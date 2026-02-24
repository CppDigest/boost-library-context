# #13 Only support std::random_shuffle when available [Merged]

> Username: danieljames  
> Created at: 2018-04-07 14:12:42 UTC  
> Updated at: 2018-04-20 03:43:01 UTC  
> Merged at: 2018-04-20 03:43:01 UTC  
> Closed at: 2018-04-20 03:43:01 UTC  
> Url: https://github.com/boostorg/lambda/pull/13  

It was removed in C++17. We could emulate it when it isn't available,  
but that doesn't seem worth the effort.

---
