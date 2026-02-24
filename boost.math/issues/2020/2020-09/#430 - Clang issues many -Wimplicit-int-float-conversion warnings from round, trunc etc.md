# #430 - Clang issues many -Wimplicit-int-float-conversion warnings from round, trunc etc [Closed]

> Username: pabristow  
> Created at: 2020-09-03 10:54:40 UTC  
> Updated at: 2023-04-05 15:17:20 UTC  
> Closed at: 2023-04-05 15:17:20 UTC  
> Url: https://github.com/boostorg/math/issues/430  

Running the Boost.math tests using Clang10.0.0, I note many warnings from all uses of round, trunc (and others?).  
  
`In file included from ..\..\..\boost/math/concepts/real_concept.hpp:28:  
..\..\..\boost/math/special_functions/round.hpp:118:12: warning: implicit conversion from 'long long' to 'long double' changes value from 9223372036854775807 to 9223372036854775808 [-Wimplicit-int-float-conversion]  
   if((r > (std::numeric_limits<boost::long_long_type>::max)()) || (r < (std::numeric_limits<boost::long_long_type>::min)()))  
         ~ ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
`  
  
Warnings only with Clang, not GCC nor MSVC, but the very many warning are cluttering the log files.  
  
Should I deal with this by quieting the warning, perhaps by adding   
  
`  
<toolset>clang:<cxxflags>-Wno-implicit-int-float-conversion  
`  
to the /test jamfile?  
  
Or is there some more subtle change required?

---

## Comment 1

> Username: evanmiller  
> Created at: 2020-12-19 21:23:18 UTC  
> Url: https://github.com/boostorg/math/issues/430#issuecomment-748527228  

I can't speak for the authors of the code but here may be some subtle issues with numbers here at the outer reaches of the `long long` type. For example, looking at the cited `llround` code:  
  
https://github.com/boostorg/math/blob/5a1a3027c8399705200988c3551cb5071eadfbb3/include/boost/math/special_functions/round.hpp#L113-L121  
  
Let's follow the logic of the warning and say that `r` comes in as `9223372036854775808`. The number `max` should be `9223372036854775807`, but according to the warning it is converted to `9223372036854775808` before comparison. The numbers compare equal, and so instead of raising an error, a static cast to `long long` is performed on `9223372036854775808`, which is unrepresentable by `long long`. The function in this case will return an undefined result.  
  
Based on the foregoing analysis, I'd say the warnings are legitimate and likely merit additional attention. The test code does appear to cover the long long max case – but it could be falling into the trap of comparing the undefined integer result of the function with the same undefined test value (`long long` max cast to a `long double`). I don't know if anyone has run an undefined behavior sanitizer on the test suite, but they can be very helpful for uncovering edge cases like these at run-time.

---

## Comment 2

> Username: evanmiller  
> Created at: 2020-12-22 17:00:02 UTC  
> Url: https://github.com/boostorg/math/issues/430#issuecomment-749658329  

See also #435

---

## Comment 3

> Username: mborland  
> Created at: 2021-01-07 16:39:12 UTC  
> Url: https://github.com/boostorg/math/issues/430#issuecomment-756230310  

This should be resolved by PR #474

---

## Comment 4

> Username: mborland  
> Created at: 2021-01-08 19:24:53 UTC  
> Url: https://github.com/boostorg/math/issues/430#issuecomment-756949776  

Found another instance stemming from beta:  
  
```  
In file included from test_t_test.cpp:13:  
In file included from ../../../boost/math/statistics/t_test.hpp:15:  
In file included from ../../../boost/math/distributions/students_t.hpp:16:  
../../../boost/math/special_functions/beta.hpp:1286:90: warning: implicit conversion from 'int' to 'float' changes value from 2147483547 to 2147483520 [-Wimplicit-const-int-float-conversion]  
         if((floor(a) == a) && (floor(b) == b) && (a < (std::numeric_limits<int>::max)() - 100) && (y != 1))  
```

---

## Comment 5

> Username: tworonix  
> Created at: 2023-03-16 21:26:07 UTC  
> Updated at: 2023-03-17 22:40:44 UTC  
> Url: https://github.com/boostorg/math/issues/430#issuecomment-1472765909  

@evanmiller was entirely correct. The warnings were legitimate and it was a mistake to silence them with static casts.  
I only found this thread and this issue after hitting this bug in production.  
  
`llround` fails to detect overflow for certain values not fitting int64_t.   
It returns an incorrect result instead of throwing an exception.   
  
All functions which convert integers max values to floating point are broken. int64_t max value can't be represented as a double. This code is incorrect: `if(r > static_cast<result_type>((std::numeric_limits<boost::long_long_type>::max)())`.  
Result of the cast will be int64::max + 1 = 2^63 (the closest representable double)  
  
Here is a failing test case https://godbolt.org/z/G7nrP9nox :  
```  
#include <boost/math/special_functions/round.hpp>  
#include <iostream>  
  
double x = 9223372036854775807.0;  // can't be represented as double, will have a different value at runtime.   
int main()  
{  
    int64_t result = boost::math::llround(x);  
    std::cout << result << std::endl;  
    if (result < 0)  
    {  
        std::cout << "FAILED\n";  
    }  
}  
```  
This program prints:  
```  
-9223372036854775808  
FAILED  
```  
Also an important fun fact: if the input is known at compile time then the above test incorrectly passes (change to `static const double x = ...` to see). Also use `9223372036854775808.0` as input. This might affect unit tests.

---

## Comment 6

> Username: mborland  
> Created at: 2023-03-16 23:32:27 UTC  
> Url: https://github.com/boostorg/math/issues/430#issuecomment-1472895295  

Interestingly we are getting the same result as the STL [https://godbolt.org/z/Ev4ManrsW](https://godbolt.org/z/Ev4ManrsW). This also does not impact ARM systems because my M1 Mac properly returns `9223372036854775807`. I'll look into it more.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2023-03-17 18:42:10 UTC  
> Url: https://github.com/boostorg/math/issues/430#issuecomment-1474260004  

@mborland this also seems to fix the issue, and is much simpler:  
  
```  
template <class T, class Policy>  
inline long long llround(const T& v, const Policy& pol)  
{  
   BOOST_MATH_STD_USING  
   using result_type = boost::math::tools::promote_args_t<T>;  
  
   result_type r = boost::math::round(v, pol);  
  
   static const result_type max_val = std::ldexp(static_cast<result_type>(1), std::numeric_limits<long long>::digits);  
  
   if (r >= max_val || r < -max_val)  
   {  
      return static_cast<long long>(boost::math::policies::raise_rounding_error("boost::math::llround<%1%>(%1%)", nullptr, v, static_cast<long long>(0), pol));  
   }  
   return static_cast<long long>(r);  
}  
```  
  
It would be nice to make `max_val` constexpr, but then we require T to be a native floating point type, we could enable_if two versions I guess.  
  
I also note that itrunc/lltrunc will presumably have the same issue, possibly also the overloads of these functions in Boost.Multiprecision - though those are much less likely to have too little precision to accurately hold a long long.

---

## Comment 8

> Username: mborland  
> Created at: 2023-03-17 18:51:05 UTC  
> Url: https://github.com/boostorg/math/issues/430#issuecomment-1474271128  

`ccmath::ldexp` takes any `Real` type. As long as `BOOST_MATH_IS_CONSTANT_EVALUATED` is defined it could be made constexpr.

---

## Comment 9

> Username: jzmaddock  
> Created at: 2023-03-17 18:55:12 UTC  
> Url: https://github.com/boostorg/math/issues/430#issuecomment-1474274871  

> ccmath::ldexp takes any Real type. As long as BOOST_MATH_IS_CONSTANT_EVALUATED is defined it could be made constexpr.  
  
Yes, but isn't this where it gets tricky?  We can't declare a variable that the result of ccmath::ldexp is assigned to as constexpr unless we know in advance that type T is capable of that.

---

## Comment 10

> Username: mborland  
> Created at: 2023-03-17 19:00:39 UTC  
> Url: https://github.com/boostorg/math/issues/430#issuecomment-1474280215  

I'm hopeful it can be macro enabled instead of dispatching through enable if. I'll play with it.

---

## Comment 11

> Username: tworonix  
> Created at: 2023-03-17 22:55:27 UTC  
> Updated at: 2023-03-17 23:06:44 UTC  
> Url: https://github.com/boostorg/math/issues/430#issuecomment-1474487836  

I appreciate that you were able to look at this so quickly, it is awesome. Thank you for this effort.  
  
Regarding the result from STL: apparently `std::llround` reports errors through `std::fetestexcept`, see here: https://godbolt.org/z/1dxTer6rq . It seems to correctly report the error.  
  
And regarding the M1: can you try with 9223372036854775808.0 ? (8 instead of 7 in the last place, this is equal to int64_max + 1). It should not be rounded down to ...7. Also try changing the variable to volatile. The compiler does something weird if the constants are known.  
  
For example, see the above godbolt link. change `double x` to `static const double x` and see that suddenly boost returns a positive result instead of negative (but still incorrect, it should throw) and STD stops reporting the error 😵‍💫 My point is that you need to be careful with unit tests to actually test the fix.

---

## Comment 12

> Username: jzmaddock  
> Created at: 2023-03-18 19:04:50 UTC  
> Url: https://github.com/boostorg/math/issues/430#issuecomment-1474957323  

Suggested test case, avoids accidental const-propogation and checks both sides of the boundary.  
  
```  
template <class Real>  
void test_llround_near_boundary()  
{  
   using std::ldexp;  
   Real boundary = ldexp(static_cast<Real>(1), std::numeric_limits<long long>::digits);  
  
   Real value;  
   int i;  
  
   for (value = boundary, i = 0; i < 100; value = boost::math::float_next(value), ++i)  
   {  
      BOOST_CHECK_THROW(boost::math::llround(value), boost::math::rounding_error);  
   }  
   for (value = boost::math::float_prior(boundary), i = 0; i < 1000; value = boost::math::float_prior(value), ++i)  
   {  
      BOOST_CHECK_EQUAL(static_cast<Real>(boost::math::llround(value)), boost::math::round(value));  
   }  
   for (value = boost::math::float_prior(-boundary), i = 0; i < 100; value = boost::math::float_prior(value), ++i)  
   {  
      BOOST_CHECK_THROW(boost::math::llround(value), boost::math::rounding_error);  
   }  
   for (value = -boundary, i = 0; i < 1000; value = boost::math::float_next(value), ++i)  
   {  
      BOOST_CHECK_EQUAL(static_cast<Real>(boost::math::llround(value)), boost::math::round(value));  
   }  
}  
```
