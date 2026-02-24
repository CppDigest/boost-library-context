# #11 Fix Miller-Rabin not recognizing 2 as prime. [Closed]

> Username: TPolzer  
> Created at: 2015-07-23 15:05:55 UTC  
> Updated at: 2015-08-10 11:51:19 UTC  
> Closed at: 2015-08-10 11:51:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/11  

miller_rabin_test returned false for all even numbers, including 2.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-08-10 11:51:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/11#issuecomment-129420102  

I fixed this by just adding a special case for n == 2: https://github.com/boostorg/multiprecision/commit/9e3115f376d01ff0e0a51d9ad0a487533c0689c2

---
