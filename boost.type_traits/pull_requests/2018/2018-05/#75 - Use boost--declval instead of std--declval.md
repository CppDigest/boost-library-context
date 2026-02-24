# #75 Use boost::declval instead of std::declval [Merged]

> Username: dsakurai  
> Created at: 2018-05-18 14:30:58 UTC  
> Updated at: 2018-05-23 16:52:14 UTC  
> Merged at: 2018-05-22 16:59:48 UTC  
> Closed at: 2018-05-22 16:59:48 UTC  
> Url: https://github.com/boostorg/type_traits/pull/75  

This pull request replaces a `std::declval` found in `is_complete.hpp` with one from Boost.  
  
std::declval is rather new, and thus it is not available on some compilers.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-05-19 19:50:50 UTC  
> Url: https://github.com/boostorg/type_traits/pull/75#issuecomment-390428396  

Is there a particular compiler configuration that's failing with this?  I ask because the the preprocessor branch that uses declval has a rather high bar anyway in BOOST_NO_CXX11_FUNCTION_TEMPLATE_DEFAULT_ARGS.

---

## Comment 2

> Username: dsakurai  
> Created_at: 2018-05-22 14:08:16 UTC  
> Url: https://github.com/boostorg/type_traits/pull/75#issuecomment-391003911  

> Is there a particular compiler configuration that's failing with this?  
  
With Intel's icc version 18 I get the following error:  
  
~~~  
$ icc -std=c++11 -fPIC -c source.cpp   
In file included from include/boost/type_traits/is_convertible.hpp(17),  
                 from include/boost/math/tools/promotion.hpp(29),  
                 from include/boost/math/special_functions/detail/round_fwd.hpp(12),  
                 from include/boost/math/special_functions/math_fwd.hpp(27),  
                 from include/boost/math/special_functions/fpclassify.hpp(19),  
                 from include/boost/random/hyperexponential_distribution.hpp(22),  
                 from include/boost/random.hpp(69),  
                 from source.cpp(14):  
include/boost/type_traits/is_complete.hpp(62): error: namespace "std" has no member "declval"  
           template <class U, class = decltype(sizeof(std::declval< U >())) >  
~~~  
(I omitted some paths for the purpose of explanation.)  
  
The git commit is a3114e9f79afe1a5ff0a80fad5d63a2f64bb9fcc.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2018-05-22 16:59:42 UTC  
> Url: https://github.com/boostorg/type_traits/pull/75#issuecomment-391065468  

New compiler, old std lib, got it.  Will merge, thanks.

---

## Comment 4

> Username: dsakurai  
> Created_at: 2018-05-23 16:52:14 UTC  
> Url: https://github.com/boostorg/type_traits/pull/75#issuecomment-391421058  

Thanks!

---
