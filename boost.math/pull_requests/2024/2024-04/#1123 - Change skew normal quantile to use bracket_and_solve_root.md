# #1123 Change skew normal quantile to use bracket_and_solve_root. [Merged]

> Username: jzmaddock  
> Created at: 2024-04-21 16:19:51 UTC  
> Updated at: 2024-05-05 07:13:06 UTC  
> Merged at: 2024-05-03 16:08:28 UTC  
> Closed at: 2024-05-03 16:08:28 UTC  
> Url: https://github.com/boostorg/math/pull/1123  

Rather than Newton iterations.  
Add test case.  
Fixes https://github.com/boostorg/math/issues/1120

---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2024-04-21 16:48:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1123#pullrequestreview-2013479672  

📁 test/git_issue_1120.cpp

```diff
  46 |+          CHECK_LE(global_iter_count, static_cast<std::uintmax_t>(55));
  47 |+          double p = cdf(dist, x);
  48 |+          CHECK_ULP_CLOSE(p, pn[i], 45000);
```

> Username: NAThompson  
> Created_at: 2024-04-21 16:48:06 UTC  
> Url: https://github.com/boostorg/math/pull/1123#discussion_r1573852549  

Given that we know that derivative of the CDF, I wonder if we could make this a bit more simple using the [condition number of rootfinding](https://fncbook.github.io/v1.0/nonlineqn/rootproblem.html), e.g.,  
  
```cpp  
CHECK_ABSOLUTE_CLOSE(p, pn[i], 1/abs(pdf(dist, x));  
```


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-04-22 15:32:06 UTC  
> Url: https://github.com/boostorg/math/pull/1123#issuecomment-2069910203  

@NAThompson : I tried using check_conditioned_error which *seems* like the right test(?) but am getting screwy results, consider:  
  
```  
      boost::math::skew_normal_distribution<double, scipy_policy> dist(0.021976066231018443, 2.6354135055511327, 437);  
```  
  
And the quantile at p = 0.0001.  bracket_and_solve_root is returning at interval with just 5 fp values in it, and we can test all of them like this:  
  
```  
int main()  
{  
   using scipy_policy = boost::math::policies::policy<boost::math::policies::promote_double<false>>;  
  
   double x = 0.014668720063513014;  
   double y = 0.014668720063513021;  
  
   while (x <= y)  
   {  
      double p = 0.0001;  
      boost::math::skew_normal_distribution<double, scipy_policy> dist(0.021976066231018443, 2.6354135055511327, 437);  
      std::cout << std::setprecision(18) << "Testing at x = " << x << "cdf - 0.0001 = " << cdf(dist, x) - p << std::endl;  
  
      double pf = cdf(dist, x);  
      CHECK_CONDITIONED_ERROR(x, p, pdf(dist, x), pf, 1.0);  
  
      x = boost::math::float_next(x);  
   }  
   return boost::math::test::report_errors();  
}  
```  
  
But this yields apparently large errors:  
  
```  
Testing at x = 0.014668720063513014cdf - 0.0001 = -1.10182045778839388e-17  
Error at D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:main:49  
  The relative error at abscissa x = 0.01466872006351301 = 0x1.e0aa2479363aap-7 in double precision is 1.10182045778839388e-13  
  This exceeds the tolerance 5.56236236314155621e-16  
  Expected: +0.00010000000000000 = +1.00000000000000005e-04 = +0x1.a36e2eb1c432dp-14  
  Computed: +0.00009999999999999 = +9.99999999999889866e-05 = +0x1.a36e2eb1c4000p-14  
  Condition number of function evaluation: 5.01013061318879682e+00 = 5.01013061318879682  
  Badness scale required to make this message go away: 198.08498365541556  
Testing at x = 0.0146687200635130157cdf - 0.0001 = -1.10182045778839388e-17  
Error at D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:main:49  
  The relative error at abscissa x = 0.01466872006351302 = 0x1.e0aa2479363abp-7 in double precision is 1.10182045778839388e-13  
  This exceeds the tolerance 5.56236236314156213e-16  
  Expected: +0.00010000000000000 = +1.00000000000000005e-04 = +0x1.a36e2eb1c432dp-14  
  Computed: +0.00009999999999999 = +9.99999999999889866e-05 = +0x1.a36e2eb1c4000p-14  
  Condition number of function evaluation: 5.01013061318880215e+00 = 5.01013061318880215  
  Badness scale required to make this message go away: 198.08498365541536  
Testing at x = 0.0146687200635130174cdf - 0.0001 = -1.10182045778839388e-17  
Error at D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:main:49  
  The relative error at abscissa x = 0.01466872006351302 = 0x1.e0aa2479363acp-7 in double precision is 1.10182045778839388e-13  
  This exceeds the tolerance 5.56236236314156410e-16  
  Expected: +0.00010000000000000 = +1.00000000000000005e-04 = +0x1.a36e2eb1c432dp-14  
  Computed: +0.00009999999999999 = +9.99999999999889866e-05 = +0x1.a36e2eb1c4000p-14  
  Condition number of function evaluation: 5.01013061318880393e+00 = 5.01013061318880393  
  Badness scale required to make this message go away: 198.0849836554153  
Testing at x = 0.0146687200635130192cdf - 0.0001 = 4.44929466533738882e-17  
Error at D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:main:49  
  The relative error at abscissa x = 0.01466872006351302 = 0x1.e0aa2479363adp-7 in double precision is 4.44929466533738882e-13  
  This exceeds the tolerance 5.56236236314156804e-16  
  Expected: +0.00010000000000000 = +1.00000000000000005e-04 = +0x1.a36e2eb1c432dp-14  
  Computed: +0.00010000000000004 = +1.00000000000044498e-04 = +0x1.a36e2eb1c5000p-14  
  Condition number of function evaluation: 5.01013061318880748e+00 = 5.01013061318880748  
  Badness scale required to make this message go away: 799.89299057900109  
Testing at x = 0.0146687200635130209cdf - 0.0001 = 4.44929466533738882e-17  
Error at D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:main:49  
  The relative error at abscissa x = 0.01466872006351302 = 0x1.e0aa2479363aep-7 in double precision is 4.44929466533738882e-13  
  This exceeds the tolerance 5.56236236314157298e-16  
  Expected: +0.00010000000000000 = +1.00000000000000005e-04 = +0x1.a36e2eb1c432dp-14  
  Computed: +0.00010000000000004 = +1.00000000000044498e-04 = +0x1.a36e2eb1c5000p-14  
  Condition number of function evaluation: 5.01013061318881192e+00 = 5.01013061318881192  
  Badness scale required to make this message go away: 799.89299057900041  
Error count: 5  
```  
  
What am I doing wrong?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2024-04-22 17:50:22 UTC  
> Url: https://github.com/boostorg/math/pull/1123#issuecomment-2070420413  

Never mind... looking at the difference (In ULP) between successive cdf values I see:  
  
```  
diff: 0  
diff: 0  
diff: 0  
diff: 4096  
diff: 0  
```  
  
The next successive diffs are all zero as well, so there's a step change somewhere in the owen's t probably.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-04-22 17:57:44 UTC  
> Url: https://github.com/boostorg/math/pull/1123#issuecomment-2070446345  

> The next successive diffs are all zero as well, so there's a step change somewhere in the owen's t probably.  
  
Ah my bad, I'd missed (forgotten) that the skew normal cdf was the difference between two values.  Sorry for the noise!

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2024-04-23 10:25:21 UTC  
> Url: https://github.com/boostorg/math/pull/1123#issuecomment-2071950447  

Anyone any ideas why clang would have *much* higher error rates - as in nearly 50eps rather than 5?  Same std lib and platform etc.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2024-04-24 11:40:54 UTC  
> Url: https://github.com/boostorg/math/pull/1123#issuecomment-2074746263  

OK, merging develop has made things even worse:  
  
* All the codecov tests are failing, @mborland any ideas?  
* There's a new set of failures for the MacOS clang tests: these look rather strange and completely random to me, I can only guess that the test system has changed somehow, is anyone in a position to investigate?  The new error rates aren't within a normal "margin or error" but quite large and effecting both double and long double.

---

## Comment 7

> Username: mborland  
> Created_at: 2024-04-24 11:51:07 UTC  
> Updated_at: 2024-04-24 11:55:57 UTC  
> Url: https://github.com/boostorg/math/pull/1123#issuecomment-2074763223  

> OK, merging develop has made things even worse:  
>   
> * All the codecov tests are failing, @mborland any ideas?  
  
Last week I noticed this across the board on boost: https://github.com/boostorg/boost-ci/issues/230. This PR contained the recommended solution: https://github.com/boostorg/math/pull/1124. That ran fine, and same with multi-precision earlier today. Now it looks like a Microsoft issue: https://github.com/boostorg/math/actions/runs/8813624024/job/24197526207?pr=1123#step:7:86  
  
> * There's a new set of failures for the MacOS clang tests: these look rather strange and completely random to me, I can only guess that the test system has changed somehow, is anyone in a position to investigate?  The new error rates aren't within a normal "margin or error" but quite large and effecting both double and long double.  
  
Homebrew added clang-18 yesterday. I see some of the same problems as https://github.com/boostorg/math/actions/runs/8813624023/job/24191756783#step:11:941 locally:  
  
```  
====== BEGIN OUTPUT ======  
Running 71 test cases...  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:19: error: in "test_autodiff_8/hermite_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:152: error: in "test_autodiff_8/laguerre_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
test_autodiff_8.cpp:157: error: in "test_autodiff_8/laguerre_hpp<long double>": check isNearZero(autodiff_v.derivative(0u) - anchor_v) has failed  
  
*** 38 failures are detected in the test module "test_autodiff"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```  
  
We also run against macOS latest so maybe they made the switch from x86_64 to ARM? Looks like it should have hit a while ago though: https://github.com/actions/runner-images/issues/9254

---

## Comment 8

> Username: mborland  
> Created_at: 2024-04-24 12:06:08 UTC  
> Url: https://github.com/boostorg/math/pull/1123#issuecomment-2074789780  

Everything running on ubuntu 22.04 is currently broken: https://github.com/orgs/community/discussions/120966

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2024-04-24 15:40:15 UTC  
> Url: https://github.com/boostorg/math/pull/1123#issuecomment-2075251094  

Thanks @mborland , looks like the MS issue is now fixed.  With regard to clang/MacOS, I wonder if you could run this instrumented version locally and see if shows up where things go wrong?  
  
```  
#define BOOST_MATH_INSTRUMENT  
  
#include <random>  
#include <boost/math/special_functions.hpp>  
#include "math_unit_test.hpp"  
  
namespace boost::math::detail {  
  
   template <class T, class Policy>  
   T heuman_lambda_imp2(T phi, T k, const Policy& pol)  
   {  
      BOOST_MATH_STD_USING  
         using namespace boost::math::tools;  
      using namespace boost::math::constants;  
  
      const char* function = "boost::math::heuman_lambda<%1%>(%1%, %1%)";  
  
      if (fabs(k) > 1)  
         return policies::raise_domain_error<T>(function, "We require |k| <= 1 but got k = %1%", k, pol);  
  
      T result;  
      T sinp = sin(phi);  
      BOOST_MATH_INSTRUMENT_VARIABLE(sinp);  
      T cosp = cos(phi);  
      BOOST_MATH_INSTRUMENT_VARIABLE(cosp);  
      T s2 = sinp * sinp;  
      BOOST_MATH_INSTRUMENT_VARIABLE(s2);  
      T k2 = k * k;  
      BOOST_MATH_INSTRUMENT_VARIABLE(k2);  
      T kp = 1 - k2;  
      BOOST_MATH_INSTRUMENT_VARIABLE(kp);  
      T delta = sqrt(1 - (kp * s2));  
      BOOST_MATH_INSTRUMENT_VARIABLE(delta);  
      if (fabs(phi) <= constants::half_pi<T>())  
      {  
         result = kp * sinp * cosp / (delta * constants::half_pi<T>());  
         result *= ellint_rf_imp(T(0), kp, T(1), pol) + k2 * ellint_rj(T(0), kp, T(1), T(1 - k2 / (delta * delta)), pol) / (3 * delta * delta);  
      }  
      else  
      {  
         typedef std::integral_constant<int,  
            std::is_floating_point<T>::value&& std::numeric_limits<T>::digits && (std::numeric_limits<T>::digits <= 54) ? 0 :  
            std::is_floating_point<T>::value&& std::numeric_limits<T>::digits && (std::numeric_limits<T>::digits <= 64) ? 1 : 2  
         > precision_tag_type;  
  
         T rkp = sqrt(kp);  
         T ratio;  
         if (rkp == 1)  
         {  
            return policies::raise_domain_error<T>(function, "When 1-k^2 == 1 then phi must be < Pi/2, but got phi = %1%", phi, pol);  
         }  
         else  
         {  
            T f = ellint_f_imp(phi, rkp, pol);  
            BOOST_MATH_INSTRUMENT_VARIABLE(f);  
            T lk = ellint_k_imp(rkp, pol, precision_tag_type());  
            BOOST_MATH_INSTRUMENT_VARIABLE(lk);  
            ratio = f / lk;  
            BOOST_MATH_INSTRUMENT_VARIABLE(ratio);  
         }  
         T lk = ellint_k_imp(k, pol, precision_tag_type());  
         T z = jacobi_zeta_imp(phi, rkp, pol);  
         BOOST_MATH_INSTRUMENT_VARIABLE(lk);  
         BOOST_MATH_INSTRUMENT_VARIABLE(z);  
         result = ratio + lk * z / constants::half_pi<T>();  
      }  
      return result;  
   }  
  
}  
  
int main()  
{  
   double x = 1.8752593994140625;  
   double y = 0.00073421024717390537;  
   double z = 1.0459920778725713;  
  
   double found = boost::math::detail::heuman_lambda_imp2(x, y, boost::math::policies::policy<>());  
  
   std::cout << std::setprecision(17) << "Found:    " << found << std::endl;  
   std::cout << std::setprecision(17) << "expected: " << z << std::endl;  
   std::cout << std::setprecision(17) << "error:    " << boost::math::relative_difference(z, found)  << std::endl;  
  
   return boost::math::test::report_errors();  
}  
```  
  
Output should be:  
  
```  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:28 sinp = 0.95400805069527894186620642358320765  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:30 cosp = -0.29978098540199993315980009356280789  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:32 s2 = 0.91013136079140588563518576847855002  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:34 k2 = 5.3906468705516722220161263123827666e-07  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:36 kp = 0.99999946093531288937583667575381696  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:38 delta = 0.29978180369774171110108795801352244  
D:\data\boost\boost\boost\math\special_functions\ellint_1.hpp:54 phi = 1.8752593994140625  
D:\data\boost\boost\boost\math\special_functions\ellint_1.hpp:55 k = 0.99999973046762014039501309525803663  
D:\data\boost\boost\boost\math\special_functions\ellint_1.hpp:56 function = boost::math::ellint_f<%1%>(%1%,%1%)  
D:\data\boost\boost\boost\math\special_functions\ellint_1.hpp:95 pi/2 = 1.5707963267948965579989817342720926  
D:\data\boost\boost\boost\math\special_functions\ellint_1.hpp:97 rphi = 0.30446307261916594200101826572790742  
D:\data\boost\boost\boost\math\special_functions\ellint_1.hpp:99 m = 1  
D:\data\boost\boost\boost\math\special_functions\ellint_1.hpp:106 rphi = 1.2663332541757306159979634685441852  
D:\data\boost\boost\boost\math\special_functions\ellint_1.hpp:117 sinp = 0.91013136079140588563518576847855002  
D:\data\boost\boost\boost\math\special_functions\ellint_1.hpp:118 cosp = 0.089868639208594169875965462779277004  
D:\data\boost\boost\boost\math\special_functions\ellint_rf.hpp:131 k = 4  
D:\data\boost\boost\boost\math\special_functions\ellint_1.hpp:131 result = -1.8745846153352667684544030635152012  
D:\data\boost\boost\boost\math\special_functions\ellint_1.hpp:140 result = 15.331436416208255479887156980112195  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:60 f = 15.331436416208255479887156980112195  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:62 lk = 8.6030105157717606800815701717510819  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:64 ratio = 1.78210132233377827049025654559955  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:68 lk = 1.5707965384851683143807576925610192  
D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp:69 z = -0.73610914525856563361827511471346952  
Found:    1.0459920778725711  
expected: 1.0459920778725713  
error:    2.122813447847949e-16  
```  
  
Thanks!

---

## Comment 10

> Username: mborland  
> Created_at: 2024-04-24 15:54:31 UTC  
> Url: https://github.com/boostorg/math/pull/1123#issuecomment-2075283756  

Results with Homebrew Clang 18.1.4 with target arm64-apple-darwin23.4.0:  
  
```  
scrap.cpp:23 sinp = 0.95400805069527894186620642358320765  
scrap.cpp:25 cosp = -0.29978098540199993315980009356280789  
scrap.cpp:27 s2 = 0.91013136079140588563518576847855002  
scrap.cpp:29 k2 = 5.3906468705516722220161263123827666e-07  
scrap.cpp:31 kp = 0.99999946093531288937583667575381696  
scrap.cpp:33 delta = 0.29978180369774171110108795801352244  
../../../boost/math/special_functions/ellint_1.hpp:54 phi = 1.8752593994140625  
../../../boost/math/special_functions/ellint_1.hpp:55 k = 0.99999973046762014039501309525803663  
../../../boost/math/special_functions/ellint_1.hpp:56 function = boost::math::ellint_f<%1%>(%1%,%1%)  
../../../boost/math/special_functions/ellint_1.hpp:95 pi/2 = 1.5707963267948965579989817342720926  
../../../boost/math/special_functions/ellint_1.hpp:97 rphi = 0.30446307261916594200101826572790742  
../../../boost/math/special_functions/ellint_1.hpp:99 m = 1  
../../../boost/math/special_functions/ellint_1.hpp:106 rphi = 1.2663332541757306159979634685441852  
../../../boost/math/special_functions/ellint_1.hpp:117 sinp = 0.91013136079140588563518576847855002  
../../../boost/math/special_functions/ellint_1.hpp:118 cosp = 0.089868639208594169875965462779277004  
../../../boost/math/special_functions/ellint_rf.hpp:131 k = 4  
../../../boost/math/special_functions/ellint_1.hpp:131 result = -1.8745846153352672125436129135778174  
../../../boost/math/special_functions/ellint_1.hpp:140 result = 15.331436416280821433133496611844748  
scrap.cpp:55 f = 15.331436416280821433133496611844748  
scrap.cpp:57 lk = 8.6030105158080445448831596877425909  
scrap.cpp:59 ratio = 1.7821013223346970910654363251524046  
scrap.cpp:63 lk = 1.5707965384851683143807576925610192  
scrap.cpp:64 z = -0.73610914525546089492991086444817483  
Found:    1.0459920778765945  
expected: 1.0459920778725713  
error:    3.846325686155698e-12  
```  
  
Apple Clang 15.0.0 on same platform  
  
```  
scrap.cpp:23 sinp = 0.95400805069527894186620642358320765  
scrap.cpp:25 cosp = -0.29978098540199993315980009356280789  
scrap.cpp:27 s2 = 0.91013136079140588563518576847855002  
scrap.cpp:29 k2 = 5.3906468705516722220161263123827666e-07  
scrap.cpp:31 kp = 0.99999946093531288937583667575381696  
scrap.cpp:33 delta = 0.29978180369774171110108795801352244  
../../../boost/math/special_functions/ellint_1.hpp:54 phi = 1.8752593994140625  
../../../boost/math/special_functions/ellint_1.hpp:55 k = 0.99999973046762014039501309525803663  
../../../boost/math/special_functions/ellint_1.hpp:56 function = boost::math::ellint_f<%1%>(%1%,%1%)  
../../../boost/math/special_functions/ellint_1.hpp:95 pi/2 = 1.5707963267948965579989817342720926  
../../../boost/math/special_functions/ellint_1.hpp:97 rphi = 0.30446307261916594200101826572790742  
../../../boost/math/special_functions/ellint_1.hpp:99 m = 1  
../../../boost/math/special_functions/ellint_1.hpp:106 rphi = 1.2663332541757306159979634685441852  
../../../boost/math/special_functions/ellint_1.hpp:117 sinp = 0.91013136079140588563518576847855002  
../../../boost/math/special_functions/ellint_1.hpp:118 cosp = 0.089868639208594169875965462779277004  
../../../boost/math/special_functions/ellint_rf.hpp:131 k = 4  
../../../boost/math/special_functions/ellint_1.hpp:131 result = -1.8745846153352672125436129135778174  
../../../boost/math/special_functions/ellint_1.hpp:140 result = 15.331436416280821433133496611844748  
scrap.cpp:55 f = 15.331436416280821433133496611844748  
scrap.cpp:57 lk = 8.6030105158080445448831596877425909  
scrap.cpp:59 ratio = 1.7821013223346970910654363251524046  
scrap.cpp:63 lk = 1.5707965384851683143807576925610192  
scrap.cpp:64 z = -0.73610914525546089492991086444817483  
Found:    1.0459920778765945  
expected: 1.0459920778725713  
error:    3.846325686155698e-12  
```  
  
GCC 13.2.0 on same platform  
  
```  
scrap.cpp:23 sinp = 0.95400805069527894186620642358320765  
scrap.cpp:25 cosp = -0.29978098540199993315980009356280789  
scrap.cpp:27 s2 = 0.91013136079140588563518576847855002  
scrap.cpp:29 k2 = 5.3906468705516722220161263123827666e-07  
scrap.cpp:31 kp = 0.99999946093531288937583667575381696  
scrap.cpp:33 delta = 0.29978180369774171110108795801352244  
../../../boost/math/special_functions/ellint_1.hpp:54 phi = 1.8752593994140625  
../../../boost/math/special_functions/ellint_1.hpp:55 k = 0.99999973046762014039501309525803663  
../../../boost/math/special_functions/ellint_1.hpp:56 function = boost::math::ellint_f<%1%>(%1%,%1%)  
../../../boost/math/special_functions/ellint_1.hpp:95 pi/2 = 1.5707963267948965579989817342720926  
../../../boost/math/special_functions/ellint_1.hpp:97 rphi = 0.30446307261916594200101826572790742  
../../../boost/math/special_functions/ellint_1.hpp:99 m = 1  
../../../boost/math/special_functions/ellint_1.hpp:106 rphi = 1.2663332541757306159979634685441852  
../../../boost/math/special_functions/ellint_1.hpp:117 sinp = 0.91013136079140588563518576847855002  
../../../boost/math/special_functions/ellint_1.hpp:118 cosp = 0.089868639208594169875965462779277004  
../../../boost/math/special_functions/ellint_rf.hpp:131 k = 4  
../../../boost/math/special_functions/ellint_1.hpp:131 result = -1.8745846153352667684544030635152012  
../../../boost/math/special_functions/ellint_1.hpp:140 result = 15.331436416208255479887156980112195  
scrap.cpp:55 f = 15.331436416208255479887156980112195  
scrap.cpp:57 lk = 8.6030105157717606800815701717510819  
scrap.cpp:59 ratio = 1.78210132233377827049025654559955  
scrap.cpp:63 lk = 1.5707965384851683143807576925610192  
scrap.cpp:64 z = -0.73610914525856563361827511471346952  
Found:    1.0459920778725711  
expected: 1.0459920778725713  
error:    2.122813447847949e-16  
```  
  
and GCC 11.4.0 same platform  
  
```  
scrap.cpp:23 sinp = 0.95400805069527894186620642358320765  
scrap.cpp:25 cosp = -0.29978098540199993315980009356280789  
scrap.cpp:27 s2 = 0.91013136079140588563518576847855002  
scrap.cpp:29 k2 = 5.3906468705516722220161263123827666e-07  
scrap.cpp:31 kp = 0.99999946093531288937583667575381696  
scrap.cpp:33 delta = 0.29978180369774171110108795801352244  
../../../boost/math/special_functions/ellint_1.hpp:54 phi = 1.8752593994140625  
../../../boost/math/special_functions/ellint_1.hpp:55 k = 0.99999973046762014039501309525803663  
../../../boost/math/special_functions/ellint_1.hpp:56 function = boost::math::ellint_f<%1%>(%1%,%1%)  
../../../boost/math/special_functions/ellint_1.hpp:95 pi/2 = 1.5707963267948965579989817342720926  
../../../boost/math/special_functions/ellint_1.hpp:97 rphi = 0.30446307261916594200101826572790742  
../../../boost/math/special_functions/ellint_1.hpp:99 m = 1  
../../../boost/math/special_functions/ellint_1.hpp:106 rphi = 1.2663332541757306159979634685441852  
../../../boost/math/special_functions/ellint_1.hpp:117 sinp = 0.91013136079140588563518576847855002  
../../../boost/math/special_functions/ellint_1.hpp:118 cosp = 0.089868639208594169875965462779277004  
../../../boost/math/special_functions/ellint_rf.hpp:131 k = 4  
../../../boost/math/special_functions/ellint_1.hpp:131 result = -1.8745846153352667684544030635152012  
../../../boost/math/special_functions/ellint_1.hpp:140 result = 15.331436416208255479887156980112195  
scrap.cpp:55 f = 15.331436416208255479887156980112195  
scrap.cpp:57 lk = 8.6030105157717606800815701717510819  
scrap.cpp:59 ratio = 1.78210132233377827049025654559955  
scrap.cpp:63 lk = 1.5707965384851683143807576925610192  
scrap.cpp:64 z = -0.73610914525856563361827511471346952  
Found:    1.0459920778725711  
expected: 1.0459920778725713  
error:    2.122813447847949e-16  
```  
  
After running GCC-13 I wondered if the new support for excess precision was making a difference, but clearly not since the result with GCC-11 is the same (and there shouldn't be since Mac ARM does not support 128-bit long double like linux ARM servers do).

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2024-04-24 16:14:35 UTC  
> Url: https://github.com/boostorg/math/pull/1123#issuecomment-2075333750  

Thanks Matt!  That narrows it down, I need to be going out now, but I'll do another reduced case tomorrow, it looks though, like ellint_rf is going bad - which would be strange given that it's all basic arithmetic in there.

---

## Comment 12

> Username: mborland  
> Created_at: 2024-04-24 16:32:14 UTC  
> Url: https://github.com/boostorg/math/pull/1123#issuecomment-2075378990  

For what it's worth @ckormanyos and I tried adding `ellint_1` to the decimal floating point library here:  https://github.com/cppalliance/decimal/pull/464. `comp_ellint_1` works fine which is only the trivial bits of ellint_rf. The error of `ellint_1` could not be tamed so there could be something nefarious going on in there.

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2024-04-25 17:50:25 UTC  
> Url: https://github.com/boostorg/math/pull/1123#issuecomment-2077836501  

> so there could be something nefarious going on in there.  
  
Maybe, it's a weird one this: I ran a fairly intensive test run comparing double to multiprecision results and could find no appreciable errors (MSVC).  However, there are some definite places where cancellation occurs - at least in the Heuman Lambda case - and running some interval arithmetic tests suggests the errors should be quite large - of the order 10^5 eps.  So it's possible that we're "passing by accident" at present, although that seems rather strange, and of course interval arithmetic is well known for producing over-large intervals :(  
  
What I'll do is prepare a separate PR that addresses the cancellation issues I can see, and hope that fixes the clang failures for this test: there are still several other tests failing, and the number increases in C++20 for some strange reason.

---

## Comment 14

> Username: codecov[bot]  
> Created_at: 2024-05-03 11:57:45 UTC  
> Updated_at: 2024-05-03 12:35:09 UTC  
> Url: https://github.com/boostorg/math/pull/1123#issuecomment-2092858927  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1123?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `95.55556%` with `2 lines` in your changes are missing coverage. Please review.  
> Project coverage is 93.69%. Comparing base [(`a243640`)](https://app.codecov.io/gh/boostorg/math/commit/a243640baddfaa318859d622fddb7abc3ba8a978?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`c61c4af`)](https://app.codecov.io/gh/boostorg/math/pull/1123?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1123/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1123?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1123   +/-   ##  
========================================  
  Coverage    93.69%   93.69%             
========================================  
  Files          770      771    +1       
  Lines        61070    61103   +33       
========================================  
+ Hits         57221    57252   +31       
- Misses        3849     3851    +2       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/math/pull/1123?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/git\_issue\_1120.cpp](https://app.codecov.io/gh/boostorg/math/pull/1123?src=pr&el=tree&filepath=test%2Fgit_issue_1120.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRfaXNzdWVfMTEyMC5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/distributions/skew\_normal.hpp](https://app.codecov.io/gh/boostorg/math/pull/1123?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fskew_normal.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvc2tld19ub3JtYWwuaHBw) | `84.57% <91.30%> (-0.15%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1123?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1123?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a243640...c61c4af](https://app.codecov.io/gh/boostorg/math/pull/1123?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 15

> Username: dschmitz89  
> Created_at: 2024-05-05 07:13:05 UTC  
> Url: https://github.com/boostorg/math/pull/1123#issuecomment-2094661302  

Thanks for the quick fix @jzmaddock, much appreciated. SciPy PR should land soon: https://github.com/scipy/scipy/pull/20643

---
