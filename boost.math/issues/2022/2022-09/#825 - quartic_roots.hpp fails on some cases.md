# #825 - quartic_roots.hpp fails on some cases [Closed]

> Username: JiayuanHuPonyai  
> Created at: 2022-09-23 08:44:40 UTC  
> Updated at: 2022-10-11 07:59:56 UTC  
> Closed at: 2022-10-08 18:56:23 UTC  
> Url: https://github.com/boostorg/math/issues/825  

Hi! I try to use the [quartic_roots()](https://github.com/boostorg/math/blob/ed01dae24893bb69c02c6d599acc74bdb8f46bda/include/boost/math/tools/quartic_roots.hpp#L54) to solve the quartic equation but found it will fail for some input. My boost version is 1.79.0. I'm not sure whether there is a bug or if I used this method in the wrong way.  
  
**Failure case:**  
Code:  
> #include "boost/math/tools/quartic_roots.hpp"  
> #include "boost/version.hpp"  
> #include "glog/logging.h"  
>   
> int main() {  
>   LOG(ERROR) << "version:" << BOOST_VERSION;  
>   double a = 1;  
>   double b = 1;  
>   double c = 1;  
>   double d = 1;  
>   double e = -4;  
>   auto roots = boost::math::tools::quartic_roots(a, b, c, d, e);  
>   LOG(ERROR) << a << "x^4 + " << b << "x^3 + " << c << "x^2 + " << d << "x + " << e << " = 0";  
>   LOG(ERROR) << "solution1: " << roots[0];  
>   LOG(ERROR) << "solution2: " << roots[1];  
>   LOG(ERROR) << "solution3: " << roots[2];  
>   LOG(ERROR) << "solution4: " << roots[3];  
>   return 0;  
> }  
  
Output:  
> E0923 16:30:46.720404 11370 test_boost.cc:12] version:107900  
> E0923 16:30:46.720527 11370 test_boost.cc:19] 1x^4 + 1x^3 + 1x^2 + 1x + -4 = 0  
> E0923 16:30:46.720541 11370 test_boost.cc:20] solution1: nan  
> E0923 16:30:46.720546 11370 test_boost.cc:21] solution2: -nan  
> E0923 16:30:46.720551 11370 test_boost.cc:22] solution3: nan  
> E0923 16:30:46.720553 11370 test_boost.cc:23] solution4: nan  
  
Obviously, 1 should be a root.  
  
**Success case:**  
Code:  
> #include "boost/math/tools/quartic_roots.hpp"  
> #include "boost/version.hpp"  
> #include "glog/logging.h"  
>   
> int main() {  
>   LOG(ERROR) << "version:" << BOOST_VERSION;  
>   double a = 0;  
>   double b = 0;  
>   double c = 1;  
>   double d = 0;  
>   double e = -1;  
>   auto roots = boost::math::tools::quartic_roots(a, b, c, d, e);  
>   LOG(ERROR) << a << "x^4 + " << b << "x^3 + " << c << "x^2 + " << d << "x + " << e << " = 0";  
>   LOG(ERROR) << "solution1: " << roots[0];  
>   LOG(ERROR) << "solution2: " << roots[1];  
>   LOG(ERROR) << "solution3: " << roots[2];  
>   LOG(ERROR) << "solution4: " << roots[3];  
>   return 0;  
> }  
  
Output:  
> E0923 16:40:17.181047 14251 test_boost.cc:12] version:107900  
> E0923 16:40:17.181432 14251 test_boost.cc:19] 0x^4 + 0x^3 + 1x^2 + 0x + -1 = 0  
> E0923 16:40:17.181464 14251 test_boost.cc:20] solution1: -1  
> E0923 16:40:17.181491 14251 test_boost.cc:21] solution2: 1  
> E0923 16:40:17.181494 14251 test_boost.cc:22] solution3: nan  
> E0923 16:40:17.181501 14251 test_boost.cc:23] solution4: nan

---

## Comment 1

> Username: NAThompson  
> Created at: 2022-09-23 15:05:57 UTC  
> Updated at: 2022-09-23 15:08:02 UTC  
> Url: https://github.com/boostorg/math/issues/825#issuecomment-1256335363  

You've passed in a quadratic polynomial which only has two roots. The two roots returned are correct, but since a std::array of length 4 is returned, it must be indicated that the rest are invalid. This is done via a NaN.

---

## Comment 2

> Username: JiayuanHuPonyai  
> Created at: 2022-09-23 15:22:41 UTC  
> Url: https://github.com/boostorg/math/issues/825#issuecomment-1256355203  

@NAThompson Thanks for your reply! I posted two cases. I think you are talking about the second case, which is the success case.   
Maybe you can take a look at the first case, which is the failure case. I mean the one that input is `1x^4 + 1x^3 + 1x^2 + 1x + -4 = 0` and output is four `NaN`.

---

## Comment 3

> Username: NAThompson  
> Created at: 2022-09-23 15:31:18 UTC  
> Url: https://github.com/boostorg/math/issues/825#issuecomment-1256364087  

@JiayuanHuPonyai : This is a bug; will investigate. The roots should be {1, -1.6506, NaN, NaN}.

---

## Comment 4

> Username: JiayuanHuPonyai  
> Created at: 2022-09-23 15:33:33 UTC  
> Url: https://github.com/boostorg/math/issues/825#issuecomment-1256366484  

Got it. Waiting for the good news.

---

## Comment 5

> Username: NAThompson  
> Created at: 2022-10-08 18:50:30 UTC  
> Url: https://github.com/boostorg/math/issues/825#issuecomment-1272377610  

@JiayuanHuPonyai : Sorry for the delay; it was a truly silly error on my part, and should be fixed [here](https://github.com/boostorg/math/pull/838)  
  
Thanks for the bug report!

---

## Comment 6

> Username: JiayuanHuPonyai  
> Created at: 2022-10-11 07:59:56 UTC  
> Url: https://github.com/boostorg/math/issues/825#issuecomment-1274256745  

Great, thanks! :grinning: @NAThompson
