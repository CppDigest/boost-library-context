# #7 Use Boost's unary function instead of STL's as it is deprecated in C++17 [Closed]

> Username: pavelkryukov  
> Created at: 2017-11-20 13:31:21 UTC  
> Updated at: 2017-11-20 14:20:04 UTC  
> Closed at: 2017-11-20 14:20:04 UTC  
> Url: https://github.com/boostorg/inspect/pull/7  



---

## Comment 1

> Username: danieljames  
> Created_at: 2017-11-20 13:53:02 UTC  
> Url: https://github.com/boostorg/inspect/pull/7#issuecomment-345701536  

Thanks, this should be fixed, but things in detail aren't meant to be public, and those classes might be removed in the future. It's probably best just to remove the inheritance. The typedefs from the base class probably aren't required, but if they are we can set them. I'll do this if you want.

---

## Comment 2

> Username: pavelkryukov  
> Created_at: 2017-11-20 14:06:36 UTC  
> Url: https://github.com/boostorg/inspect/pull/7#issuecomment-345705079  

Yes, please fix it. I still get compilation errors as MS Visual Studio 2017 removes std::unary_function, I'm just searching through the Boost to find libraries where it is not removed yet.

---

## Comment 3

> Username: danieljames  
> Created_at: 2017-11-20 14:20:04 UTC  
> Url: https://github.com/boostorg/inspect/pull/7#issuecomment-345708837  

Okay, it's fixed in develop. It might not be included in the next release though.

---
