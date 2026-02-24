# #1028 - C++23 std::numeric_limits<T>::has_denorm warning [Closed]

> Username: mborland  
> Created at: 2023-09-08 10:02:05 UTC  
> Updated at: 2023-10-30 08:38:13 UTC  
> Closed at: 2023-10-30 08:38:13 UTC  
> Url: https://github.com/boostorg/math/issues/1028  

The latest visual studio marked `has_denorm` deprecated so we get a new warning:  
  
```  
C:\Program Files\Microsoft Visual Studio\2022\Enterprise\VC\Tools\MSVC\14.37.32822\include\limits(40,18): note: 'float_denorm_style' has been explicitly marked deprecated here  
_EXPORT_STD enum _CXX23_DEPRECATE_DENORM float_denorm_style {  
                 ^  
C:\Program Files\Microsoft Visual Studio\2022\Enterprise\VC\Tools\MSVC\14.37.32822\include\yvals_core.h(1440,7): note: expanded from macro '_CXX23_DEPRECATE_DENORM'  
    [[deprecated("warning STL4042: "                                                                                   \  
      ^  
In file included from libs\decimal\test\test_decimal32_cmath.cpp:8:  
.\boost/math/special_functions/next.hpp(112,129): error: 'has_denorm' is deprecated: warning STL4042: std::float_denorm_style, std::numeric_limits::has_denorm, and std::numeric_limits::has_denorm_loss are deprecated in C++23. You can define _SILENCE_CXX23_DENORM_DEPRECATION_WARNING or _SILENCE_ALL_CXX23_DEPRECATION_WARNINGS to suppress this warning. [-Werror,-Wdeprecated-declarations]  
   return get_smallest_value<T>(std::integral_constant<bool, std::numeric_limits<T>::is_specialized && (std::numeric_limits<T>::has_denorm == std::denorm_present)>());  
                                                                                                                                ^  
.\boost/math/special_functions/next.hpp(492,74): note: in instantiation of function template specialization 'boost::math::detail::get_smallest_value<float>' requested here  
      return 1 + fabs(float_distance(static_cast<T>((b < 0) ? T(-detail::get_smallest_value<T>()) : detail::get_smallest_value<T>()), b, pol));  
                                                                         ^  
.\boost/math/special_functions/next.hpp(710,22): note: in instantiation of function template specialization 'boost::math::detail::float_distance_imp<float, boost::math::policies::policy<>>' requested here  
      return detail::float_distance_imp(detail::normalize_value(static_cast<result_type>(a), typename detail::has_hidden_guard_digits<result_type>::type()), detail::normalize_value(static_cast<result_type>(b), typename detail::has_hidden_guard_digits<result_type>::type()), std::integral_constant<bool, !std::numeric_limits<result_type>::is_specialized || (std::numeric_limits<result_type>::radix == 2)>(), pol);  
                     ^  
.\boost/math/special_functions/next.hpp(717,24): note: in instantiation of function template specialization 'boost::math::float_distance<float, float, boost::math::policies::policy<>>' requested here  
   return boost::math::float_distance(a, b, policies::policy<>());  
```  
  
Since this is deprecated should we remove the check for standards > C++20?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-09-08 12:48:53 UTC  
> Url: https://github.com/boostorg/math/issues/1028#issuecomment-1711617731  

Sigh... this is going to get a lot more complex, given that it appears not to have been replaced by anything?  We do have a small amount of code in there to handle SSE2 denorms at runtime, but this change now renders every CPU and platform as having unknown behaviour.  But I guess maybe it was always that way :(  
  
We need to handle this carefully to avoid breaking stuff, not sure how at present, and I'm off on holiday for a couple of weeks shortly...

---

## Comment 2

> Username: ryanelandt  
> Created at: 2023-09-11 21:20:22 UTC  
> Url: https://github.com/boostorg/math/issues/1028#issuecomment-1714595652  

BTW, I made an attempt to address this issue with #1029.
