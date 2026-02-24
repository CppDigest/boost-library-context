# #807 Don't inline is_value_non_zero () to be evaluated at runtime. [Closed]

> Username: dlaugt  
> Created at: 2022-07-26 18:31:22 UTC  
> Updated at: 2022-07-27 13:44:22 UTC  
> Closed at: 2022-07-27 13:44:21 UTC  
> Url: https://github.com/boostorg/math/pull/807  

I'm compiling a program using boost math (1.79.0) with gcc 7.3.1. When I run the program with Valgrind, I have an overflow exception at startup. The following patch fixes the issue.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-07-27 10:14:18 UTC  
> Url: https://github.com/boostorg/math/pull/807#issuecomment-1196537844  

Ugh, that's the correct fix, but we need to tweak it to maintain compatibility with standalone mode.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-07-27 13:44:21 UTC  
> Url: https://github.com/boostorg/math/pull/807#issuecomment-1196782008  

Fixed in linked PR above, closing this one.

---
