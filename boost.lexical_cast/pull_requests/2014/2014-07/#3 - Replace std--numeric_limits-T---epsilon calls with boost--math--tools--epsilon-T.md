# #3 Replace std::numeric_limits<T>::epsilon calls with boost::math::tools::epsilon<T> [Closed]

> Username: harris-m  
> Created at: 2014-07-02 16:24:57 UTC  
> Updated at: 2014-07-03 14:42:18 UTC  
> Closed at: 2014-07-03 14:42:18 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/3  

In lexical_cast_float_types_test, std::numeric_limits<long double>::epsilon() is used as a tolerance value when comparing long double values to check the accuracy of lexical_cast.  
  
On PowerPC, this epsilon value is extremely small for the long double type. While the value is technically correct, since adding 1.0L to it does in fact result in a distinct value, it is not suitable for use as a tolerance value in this type of comparison. Instead, we need a value such that:  
  
x + x \* eps != x for all x  
  
This value can be found by calculating 2^(1-D), where D is the number of bits of precision in the type.  
  
We found this same issue in many parts of the Boost math library, and resolved it by adding a special case of boost::math::tools::epsilon<long double> for platforms that use the strange "double-double" implementation of long double, such as PowerPC. This change is now in the master branch and can be seen here:  
  
https://github.com/boostorg/math/blob/7cb3316d0616db01f6c4a56126d4bf8c73e7b50d/include/boost/math/tools/precision.hpp#L160-178  
  
This pull request replaces all instances of std::numeric_limits<T>::epsilon() with boost::math::tools::epsilon<T> in lexical_cast_float_types_test. This change fixes 5222 of the 5255 failures that currently occur within the test on a PowerPC machine with long double support enabled.

---

## Comment 1

> Username: harris-m  
> Created_at: 2014-07-02 16:28:08 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/3#issuecomment-47799400  

This issue can also be found on trac:  
  
https://svn.boost.org/trac/boost/ticket/10173

---

## Comment 2

> Username: apolukhin  
> Created_at: 2014-07-03 05:13:23 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/3#issuecomment-47867467  

Patch is good, great thanks for the work!  
  
But please, make the pull request to the `develop` branch, not the `master`.

---

## Comment 3

> Username: harris-m  
> Created_at: 2014-07-03 14:42:18 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/3#issuecomment-47938331  

You got it, see #5.

---
