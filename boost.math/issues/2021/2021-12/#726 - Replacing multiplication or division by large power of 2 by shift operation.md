# #726 - Replacing multiplication or division by large power of 2 by shift operation [Closed]

> Username: GYuvanShankar  
> Created at: 2021-12-21 13:44:49 UTC  
> Updated at: 2021-12-26 05:58:58 UTC  
> Closed at: 2021-12-26 05:58:58 UTC  
> Url: https://github.com/boostorg/math/issues/726  

Will it make the code run faster by any chance? Especially when such a operation occurs in a loop.

---

## Comment 1

> Username: NAThompson  
> Created at: 2021-12-22 07:45:05 UTC  
> Url: https://github.com/boostorg/math/issues/726#issuecomment-999354838  

No, the compiler will get it. A good reference for how advanced this functionality is can be found [here](https://lemire.me/blog/2021/04/28/ideal-divisors-when-a-division-compiles-down-to-just-a-multiplication/).
