# #184 - Quantile for skew_normal returns invalid value [Closed]

> Username: yurybura  
> Created at: 2019-02-18 13:20:09 UTC  
> Updated at: 2024-02-06 12:35:15 UTC  
> Closed at: 2024-02-06 12:34:40 UTC  
> Url: https://github.com/boostorg/math/issues/184  

Code example:  
```cpp  
#include <boost/math/distributions/skew_normal.hpp>  
#include <cstdlib>  
  
int main()  
{  
  boost::math::skew_normal skew(573.39724735636185, 77.0, 4.0);  
  const double x = boost::math::quantile(skew, 0.00285612015554148);  
  const double y = boost::math::quantile(skew, 0.00285612015554149);  
  const double z = boost::math::quantile(skew, 0.00285612015554150);  
  std::printf("x=%g\n", x);  
  std::printf("y=%g\n", y);  
  std::printf("z=%g\n", z);  
}  
```  
Result on Windows 10 + MSVC 19.16.27026.1 + Boost 1.67.0:  
```  
x=539.858  
y=-inf  
z=539.858  
```  
Result on Windows 10 + MSVC 19.16.27026.1 + Boost 1.69.0:  
```  
x=539.858  
y=0  
z=539.858  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-02-18 19:26:01 UTC  
> Updated at: 2019-02-18 19:35:50 UTC  
> Url: https://github.com/boostorg/math/issues/184#issuecomment-464850493  

I just ran your code on develop, with both g++-8 and Apple clang. I get:  
  
```  
x=539.858  
y=539.858  
z=539.858  
```  
  
Could you clone the `develop` and see if it's still there in your environment?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-02-18 19:51:01 UTC  
> Url: https://github.com/boostorg/math/issues/184#issuecomment-464856567  

I can reproduce, it's a numerical instability that triggers a bug in the Newton-Raphson code, I'm just not sure at present if this is a one off or part of a larger issue...

---

## Comment 3

> Username: yurybura  
> Created at: 2019-02-19 12:52:45 UTC  
> Url: https://github.com/boostorg/math/issues/184#issuecomment-465116900  

Test project: https://github.com/yurybura/test1  
Build log: https://ci.appveyor.com/project/yurybura/test1

---

## Comment 4

> Username: pabristow  
> Created at: 2019-02-19 15:03:49 UTC  
> Url: https://github.com/boostorg/math/issues/184#issuecomment-465165477  

I can also confirm and that it goes wrong before or here  
delta becomes inf while delta1 and delta2 is 1.344e-14  
  
     if(fabs(delta * 2) > fabs(delta2))  
      {  
         // last two steps haven't converged.  
         delta = (delta > 0) ? (result - min) / 2 : (result - max) / 2;  <<<<<  
         if (fabs(delta) > fabs(result))  
            delta = sign(delta) * result; // protect against huge jumps!  
         // reset delta2 so we don't take this branch next time round:  
         delta1 = delta;  
         delta2 = 3 * delta;  
      }  
  
at iteration down to 192 (from a very large max_iterations estimate of 200 - is this right?  
  
but I am not expert enough to see why and how to correct it.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2019-02-20 20:53:39 UTC  
> Url: https://github.com/boostorg/math/issues/184#issuecomment-465749583  

Fixed in develop in the commit referenced above.

---

## Comment 6

> Username: yurybura  
> Created at: 2024-01-25 10:57:26 UTC  
> Url: https://github.com/boostorg/math/issues/184#issuecomment-1909917413  

Issue is returned in Boost 1.84.0.   
The example returns on MSVC 19.38.33134.0 + Boost 1.84.0:  
```  
x=539.858  
y=-1.43195e+250  
z=539.858  
```

---

## Comment 7

> Username: jzmaddock  
> Created at: 2024-01-25 19:10:44 UTC  
> Url: https://github.com/boostorg/math/issues/184#issuecomment-1910821601  

Confirmed, though strangely the cause is different this time.  
  
Things to check and improve:  
  
1) The derivative is going to zero - why?  
2) The zero-derivative handling code in newton_raphson_iterate needs to be updated along the same lines as the previous fix.  
3) The quantile function has no error handling for exhausting the max root iterations.  
4) We can probably narrow the search bounds anyway before iterating.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2024-01-25 19:23:07 UTC  
> Url: https://github.com/boostorg/math/issues/184#issuecomment-1910838661  

OK, ignore (1) and (2), this got reverted as part of https://github.com/boostorg/math/pull/1002 which was evidently a bad idea.  (3) and (4) we should still look at.

---

## Comment 9

> Username: jzmaddock  
> Created at: 2024-02-06 12:35:13 UTC  
> Url: https://github.com/boostorg/math/issues/184#issuecomment-1929435331  

This should now be fixed again, with better testing this time!
