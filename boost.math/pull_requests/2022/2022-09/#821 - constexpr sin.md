# #821 constexpr sin [Closed]

> Username: mborland  
> Created at: 2022-09-04 16:38:52 UTC  
> Updated at: 2024-02-22 10:28:53 UTC  
> Closed at: 2024-02-22 10:28:53 UTC  
> Url: https://github.com/boostorg/math/pull/821  



---

## Comment 1

> Username: mborland  
> Created_at: 2022-09-07 23:26:06 UTC  
> Url: https://github.com/boostorg/math/pull/821#issuecomment-1240015778  

@NAThompson Will you take a look at this? I think within an epsilon for floats and doubles at compile time is reasonable accuracy.

---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2022-09-08 03:10:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/821#pullrequestreview-1100036993  

📁 include/boost/math/ccmath/sin.hpp

```diff
  40 |+     const T x9 = x8 * x;
  41 |+ 
  42 |+     const T A = x3 * (a1 + x2 * (a2 + x2 * a3));
```

> Username: NAThompson  
> Created_at: 2022-09-08 03:10:44 UTC  
> Url: https://github.com/boostorg/math/pull/821#discussion_r965461931  

Is the fma constexpr? If so, you might be able to do a `std::fma` here and improve the accuracy.


---

## Comment 3

> Username: NAThompson  
> Created_at: 2022-09-08 03:11:53 UTC  
> Url: https://github.com/boostorg/math/pull/821#issuecomment-1240164435  

Did you create an ULPs plot for this? Might be able to find some regions of lower accuracy. . . .

---

## Review 4 [Commented]

> Username: NAThompson  
> Created_at: 2022-09-08 03:12:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/821#pullrequestreview-1100038112  

📁 include/boost/math/ccmath/sin.hpp

```diff
  23 |+ [[nodiscard]] constexpr T sin_impl(T x) noexcept
  24 |+ {
  25 |+     // Precalculated coeffs from the minmax polynomial for up to 128 bit long doubles
```

> Username: NAThompson  
> Created_at: 2022-09-08 03:12:50 UTC  
> Url: https://github.com/boostorg/math/pull/821#discussion_r965462685  

Are your long doubles 128 bits?

> Username: mborland  
> Created_at: 2022-09-08 23:53:49 UTC  
> Url: https://github.com/boostorg/math/pull/821#discussion_r966510495  

No but they have sufficient precision. -mlong-double-128 is not supported on x86_64 because it implicitly calls `__float128`. In order to support that I would have to make the end user link `-lquadmath` anytime these functions are used.


---

## Comment 5

> Username: mborland  
> Created_at: 2022-09-08 23:47:33 UTC  
> Url: https://github.com/boostorg/math/pull/821#issuecomment-1241343837  

>   
  
Is there a way to generate them with constexpr functions?

---

## Comment 6

> Username: NAThompson  
> Created_at: 2022-09-09 01:48:09 UTC  
> Url: https://github.com/boostorg/math/pull/821#issuecomment-1241408070  

@mborland : You're worried that the constexpr branch will do something different than the runtime branch?

---

## Comment 7

> Username: mborland  
> Created_at: 2022-09-09 02:01:29 UTC  
> Url: https://github.com/boostorg/math/pull/821#issuecomment-1241413999  

> @mborland : You're worried that the constexpr branch will do something different than the runtime branch?  
  
Yes; I default to the linked standard library for run time implementation.

---

## Comment 8

> Username: NAThompson  
> Created_at: 2022-09-09 02:08:01 UTC  
> Url: https://github.com/boostorg/math/pull/821#issuecomment-1241417005  

Well, you couldn't use our standard `ulps_plot` then, but you could generate a large `std::array` and then compute `sin(array)` at compile-time, and then compute the float distance to the value computed in higher precision.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2022-09-09 17:55:21 UTC  
> Url: https://github.com/boostorg/math/pull/821#issuecomment-1242298084  

A quick check with:  
  
```  
#include <boost/math/ccmath/sin.hpp>  
#include <iostream>  
#include <iomanip>  
  
int main()  
{  
   constexpr double v = boost::math::ccmath::sin(3.1277929220348599);  
   double x = std::sin(3.1277929220348599);  
   std::cout << std::setprecision(18);  
   std::cout << v << std::endl;  
   std::cout << x << std::endl;  
   return 0;  
}  
```  
  
shows large errors as the input approaches +-PI.  
  
Unfortunately, correct angle reduction is really hard, and requires an arbitrary number of digits for pi.  However, maybe we could at least do better in the range [-PI, +PI] by storing more digits for argument reduction as a pair of values?

---

## Comment 10

> Username: mborland  
> Created_at: 2022-12-15 02:35:40 UTC  
> Url: https://github.com/boostorg/math/pull/821#issuecomment-1352479615  

The accuracy has been improved now with argument reduction. Below is the plot for `boost::math::ccmath::sin - std::sin` with the x axis being fractions of pi and y axis being form -1 epsilon to 1 epsilon for double precision types. The average difference between the two is: -0.0574961 epsilon.  
  
![sin_plot](https://user-images.githubusercontent.com/3745830/207758590-9e76c2b5-bb79-40c6-84cc-748b9724cebe.jpg)

---

## Comment 11

> Username: NAThompson  
> Created_at: 2023-01-26 18:26:49 UTC  
> Url: https://github.com/boostorg/math/pull/821#issuecomment-1405422726  

LGTM. Might want to include that ULPS plot in the docs just so no one is surprised.  
  
@jzmaddock : You agree?

---

## Comment 12

> Username: mborland  
> Created_at: 2024-02-22 10:28:53 UTC  
> Url: https://github.com/boostorg/math/pull/821#issuecomment-1959147145  

Better versions of this exist in the decimal repo. Need to port those over at some point.

---
