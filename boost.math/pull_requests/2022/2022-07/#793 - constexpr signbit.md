# #793 constexpr signbit [Merged]

> Username: mborland  
> Created at: 2022-07-01 15:58:17 UTC  
> Updated at: 2022-07-02 18:47:07 UTC  
> Merged at: 2022-07-02 18:47:03 UTC  
> Closed at: 2022-07-02 18:47:03 UTC  
> Url: https://github.com/boostorg/math/pull/793  



---

## Comment 1

> Username: mborland  
> Created_at: 2022-07-02 02:40:58 UTC  
> Url: https://github.com/boostorg/math/pull/793#issuecomment-1172818767  

@NAThompson This is good for review. I deviate slightly from the C++ standard (same as copysign) in that using UB to find the sign of a `NAN` is not allowable in constexpr contexts.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-07-02 11:21:31 UTC  
> Url: https://github.com/boostorg/math/pull/793#issuecomment-1172882020  

That all looks fine to me, I wonder how useful it will be if we can't detect signed zero though?  
  
Hmm... just a thought, we can detect signed infinities right?  So how about checking the sign of `1/x` when x is zero?  Might need to verify that the type has infinities first, and I worry it might trigger compiler traps, but would make the facility more generally useful.

---

## Comment 3

> Username: mborland  
> Created_at: 2022-07-02 15:01:29 UTC  
> Url: https://github.com/boostorg/math/pull/793#issuecomment-1172912419  

> That all looks fine to me, I wonder how useful it will be if we can't detect signed zero though?  
>   
> Hmm... just a thought, we can detect signed infinities right? So how about checking the sign of `1/x` when x is zero? Might need to verify that the type has infinities first, and I worry it might trigger compiler traps, but would make the facility more generally useful.  
  
We can detect signed infinities, but division by 0 is not allowed in constexpr contexts because it is UB.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2022-07-02 17:46:53 UTC  
> Url: https://github.com/boostorg/math/pull/793#issuecomment-1172935010  

>We can detect signed infinities, but division by 0 is not allowed in constexpr contexts because it is UB.  
  
Oh :(  I knew there had to be a catch somewhere.  Oh well, never mind, carry on as you were ;)

---
