# #782 Fix for issue 773 [Merged]

> Username: mborland  
> Created at: 2022-04-18 02:10:20 UTC  
> Updated at: 2022-04-18 17:56:39 UTC  
> Merged at: 2022-04-18 16:23:20 UTC  
> Closed at: 2022-04-18 16:23:21 UTC  
> Url: https://github.com/boostorg/math/pull/782  



---

## Comment 1

> Username: mborland  
> Created_at: 2022-04-18 14:47:10 UTC  
> Url: https://github.com/boostorg/math/pull/782#issuecomment-1101465983  

@NAThompson do you concur with this handling of NaNs? I thought this would be much lighter weigh than throwing an exception.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2022-04-18 15:51:01 UTC  
> Url: https://github.com/boostorg/math/pull/782#issuecomment-1101515916  

@mborland : In computer graphics, nans always render black. So this is the correct behavior.  
  
Why doesn't the std::isnan from std work?

---

## Comment 3

> Username: mborland  
> Created_at: 2022-04-18 15:58:18 UTC  
> Updated_at: 2022-04-18 15:58:44 UTC  
> Url: https://github.com/boostorg/math/pull/782#issuecomment-1101520682  

> Why doesn't the std::isnan from std work?  
  
Isn't the behavior of `boost::math::isnan` more stable across compilers/optimizations? Passing a NaN to `std::clamp` should be UB since `clamp` requires the less than comparable concept. I was trying to avoid this with configurations that replace `std::isnan` with `false`.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2022-04-18 16:23:15 UTC  
> Url: https://github.com/boostorg/math/pull/782#issuecomment-1101540965  

>  I was trying to avoid this with configurations that replace std::isnan with false.  
  
Oh yeah that's just awful. . .

---
