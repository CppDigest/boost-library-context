# #746 - VS 2017 compiler warnings: This header is deprecated [Closed]

> Username: vishnu114  
> Created at: 2020-08-17 23:23:15 UTC  
> Updated at: 2020-09-01 00:39:19 UTC  
> Closed at: 2020-09-01 00:39:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/746  

Several boost_1_74 geometry .hpp files warn about using deprecated headers with VS2017. Please fix these by using the recommended headers. To reproduce this, use the command below.  
  
```  
cl -MP -std:c++17 -EHsc -permissive- -Zc:twoPhase-   -D_CRT_SECURE_NO_DEPRECATE -D_CRT_NONSTDC_NO_DEPRECATE  -D_USE_MATH_DEFINES -DNOMINMAX -DWIN32_LEAN_AND_MEAN -Dstrcasecmp=_stricmp -Dstrncasecmp=_strnicmp -we4311 -we4473 -we4474 -we4477 -MD -O2 -nologo -W4 -wd4100 -wd4127 -wd4706 -wd5033 -wd6326 -wd6993  -D_SCL_SECURE_NO_WARNINGS  -D_HAS_AUTO_PTR_ETC=1  -D_SILENCE_CXX17_ALLOCATOR_VOID_DEPRECATION_WARNING -Zc:strictStrings- -Zc:ternary- -wd4244 -ID:\boost_1_74_0 -DBOOST_ALL_NO_LIB -DH5_BUILT_AS_DYNAMIC_LIB  -c -Fowin64\  test.cpp  
```  
  
where test.cpp has 3 lines:  
  
```  
//#define BOOST_ALLOW_DEPRECATED_HEADERS  
#include <boost/geometry/multi/multi.hpp> // for multi_polygon  
#include <boost/geometry/io/wkt/wkt.hpp>  
```  
  
The resulting warnings are  
  
```  
D:\boost_1_74_0\boost/range/result_iterator.hpp(20): note: This header is deprecated. Use <boost/range/iterator.hpp> instead.  
D:\boost_1_74_0\boost/range/const_reverse_iterator.hpp(20): note: This header is deprecated. Use <boost/range/reverse_iterator.hpp> instead.  
D:\boost_1_74_0\boost/range/reverse_result_iterator.hpp(20): note: This header is deprecated. Use <boost/range/reverse_iterator.hpp> instead.  
D:\boost_1_74_0\boost/detail/no_exceptions_support.hpp(17): note: This header is deprecated. Use <boost/core/no_exceptions_support.hpp> instead.  
```  
  
Uncommenting the commented line silences the warnings but the new headers should be used in an updated version of boost geometry.

---

## Comment 1

> Username: mloskot  
> Created at: 2020-08-18 09:37:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/746#issuecomment-675374933  

@vishnu114   
> Please fix these by using the recommended headers.   
  
Please, consider proposing a PR with necessary fixes.

---

## Comment 2

> Username: awulkiew  
> Created at: 2020-08-30 02:26:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/746#issuecomment-683367636  

Thanks for the report.  
  
None of the Boost.Range files you listed is included by Boost.Geometry directly. These files are included by `boost/range/metafunctions.hpp` which is included by `boost/range.hpp`. So the problem is in Boost.Range not in Boost.Geometry. You may consider creating an issue or proposing a PR there.  
  
`boost/detail/no_exceptions_support.hpp` was indeed included, which is fixed now:  
https://github.com/boostorg/geometry/commit/8deda7466ccdc846cfd65a4de89f0bc4061ae319  
  
Btw, `multi_polygon` is defined in `boost/geometry/geometries/multi_polygon.hpp`. Algorithms for multi geometries are included together with algorithms for other geometries. `boost/geometry/multi/multi.hpp` was left only for backward compatibility and it includes the whole library. So you can remove this include from your code or replace it with something else if needed.

---

## Comment 3

> Username: awulkiew  
> Created at: 2020-09-01 00:38:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/746#issuecomment-684122044  

The PR is already created in Boost.Range:  
https://github.com/boostorg/range/pull/109
