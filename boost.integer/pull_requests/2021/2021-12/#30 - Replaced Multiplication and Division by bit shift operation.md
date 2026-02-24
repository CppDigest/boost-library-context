# #30 Replaced Multiplication and Division by bit shift operation [Closed]

> Username: GYuvanShankar  
> Created at: 2021-12-21 13:26:01 UTC  
> Updated at: 2021-12-21 14:09:09 UTC  
> Closed at: 2021-12-21 14:06:22 UTC  
> Url: https://github.com/boostorg/integer/pull/30  



---

## Comment 1

> Username: Lastique  
> Created_at: 2021-12-21 13:47:14 UTC  
> Url: https://github.com/boostorg/integer/pull/30#issuecomment-998793062  

On any remotely decent compiler there'll be no performance difference. Also compile-time constant expressions don't affect runtime performance.

---

## Comment 2

> Username: GYuvanShankar  
> Created_at: 2021-12-21 13:52:25 UTC  
> Url: https://github.com/boostorg/integer/pull/30#issuecomment-998796659  

Oh alright, thanks. Will there be any difference, if the said operation is in a loop? Or if the divisor is a higher power of two?

---

## Comment 3

> Username: Lastique  
> Created_at: 2021-12-21 14:02:04 UTC  
> Updated_at: 2021-12-21 14:09:09 UTC  
> Url: https://github.com/boostorg/integer/pull/30#issuecomment-998803811  

Any compiler will replace integer division by a power of two with a shift. Plus maybe a check whether the dividend is negative, if the compiler can't deduce that this is never a case. If dividend is never negative, it would make sense to make it `unsigned`, which would reliably eliminate the check.  
  
For signed integers, it is generally preferred to use arithmetic operators rather than bitwise because of this difference wrt. negative values and because prior to C++20 signed integers could have non-two's-complement representation.

---

## Comment 4

> Username: GYuvanShankar  
> Created_at: 2021-12-21 14:06:22 UTC  
> Url: https://github.com/boostorg/integer/pull/30#issuecomment-998807128  

Thanks. Will close this PR

---
