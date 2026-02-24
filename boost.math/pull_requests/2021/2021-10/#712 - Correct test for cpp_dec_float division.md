# #712 Correct test for cpp_dec_float division. [Merged]

> Username: jzmaddock  
> Created at: 2021-10-23 16:21:11 UTC  
> Updated at: 2021-10-24 15:57:05 UTC  
> Merged at: 2021-10-24 12:00:16 UTC  
> Closed at: 2021-10-24 12:00:16 UTC  
> Url: https://github.com/boostorg/math/pull/712  

@ckormanyos , @mborland : this should fix the current quaternion / cpp_dec_float failures.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-10-24 07:32:25 UTC  
> Updated_at: 2021-10-24 07:34:28 UTC  
> Url: https://github.com/boostorg/math/pull/712#issuecomment-950276045  

> this should fix the current quaternion / `cpp_dec_float` failures.  
  
Also note that this [recent Multiprecision PR](https://github.com/boostorg/multiprecision/pull/371) also fixes these tests without disabling them for `cpp_dec_float`. That Multiprecision PR, however, only _partially_ addresses the missing rounding issue in `cpp_dec_float`.  
  
We could consider reviving Multiprecision's PR, synchronizing develop's new standalone changes into it and going forward with that PR. It did pass CI. On the other hand, I did list several reservations that I have about only partially addressing the rounding issue. These are in my final post in that PR.  
  
I could be happy with either: go forward with Math's PR here that disables the tests, or move forward on [Multiprecision's PR](https://github.com/boostorg/multiprecision/pull/371).  
  
See also the [relevant comments](https://github.com/boostorg/multiprecision/pull/371#issuecomment-928136309)  
  
Cc: @jzmaddock and @mborland

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-10-24 12:00:11 UTC  
> Url: https://github.com/boostorg/math/pull/712#issuecomment-950311578  

@ckormanyos I'm easy either way, for now I really just want CI clean again so we can better see where we are ;)

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2021-10-24 14:04:59 UTC  
> Updated_at: 2021-10-24 14:05:15 UTC  
> Url: https://github.com/boostorg/math/pull/712#issuecomment-950331164  

> @ckormanyos I'm easy either way, for now I really just want CI clean again so we can better see where we are  
  
Indeed @jzmaddock. Let's go that way since there has really been a lot going on.  
  
Maybe we can pick up rounding with a more strong effort in 1.79 or 1.80, as I could not fully figure out how to do it by myself. So for the moment, I'm just happy if everything works. I will let the multiprecision PR ripen, maybe synchronize with develop so as not to get too far behind.

---
