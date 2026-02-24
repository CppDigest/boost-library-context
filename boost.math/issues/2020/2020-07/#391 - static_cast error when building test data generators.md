# #391 - static_cast error when building test data generators [Closed]

> Username: evanmiller  
> Created at: 2020-07-05 04:11:47 UTC  
> Updated at: 2020-07-09 18:07:48 UTC  
> Closed at: 2020-07-09 17:39:15 UTC  
> Url: https://github.com/boostorg/math/issues/391  

I'm trying to build the test data generators in `math/tools`. I'm running the command `../../../b2` in that directory. However, the command just spews out errors such as:  
  
```  
In file included from tgamma_ratio_data.cpp:6:  
In file included from ../../../boost/math/special_functions/gamma.hpp:23:  
In file included from ../../../boost/math/constants/constants.hpp:14:  
In file included from ../../../boost/math/tools/convert_from_string.hpp:15:  
In file included from ../../../boost/lexical_cast.hpp:32:  
In file included from ../../../boost/lexical_cast/try_lexical_convert.hpp:44:  
In file included from ../../../boost/lexical_cast/detail/converter_lexical.hpp:54:  
In file included from ../../../boost/lexical_cast/detail/converter_lexical_streams.hpp:63:  
In file included from ../../../boost/lexical_cast/detail/inf_nan.hpp:35:  
In file included from ../../../boost/math/special_functions/fpclassify.hpp:17:  
../../../boost/math/tools/real_cast.hpp:22:15: error: no matching conversion for static_cast from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<1000, boost::multiprecision::backends::digit_base_2, void, int, 0, 0>, boost::multiprecision::et_off>' to 'float'  
       return static_cast<To>(t);  
              ^~~~~~~~~~~~~~~~~~  
../include_private/boost/math/tools/test_data.hpp:530:65: note: in instantiation of function template specialization 'boost::math::tools::real_cast<float, boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<1000, boost::multiprecision::backends::digit_base_2, void, int, 0, 0>, boost::multiprecision::et_off> >' requested here  
         random_ns::uniform_real_distribution<random_type> ur_a(real_cast<random_type>(arg1.z1), real_cast<random_type>(arg1.z2));  
                                                                ^  
../include_private/boost/math/tools/test_data.hpp:249:7: note: in instantiation of member function 'boost::math::tools::test_data<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<1000, boost::multiprecision::backends::digit_base_2, void, int, 0, 0>, boost::multiprecision::et_off> >::create_test_points' requested here  
      create_test_points(points1, arg1);  
      ^  
tgamma_ratio_data.cpp:52:15: note: in instantiation of function template specialization 'boost::math::tools::test_data<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<1000, boost::multiprecision::backends::digit_base_2, void, int, 0, 0>, boost::multiprecision::et_off> >::insert<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<1000, boost::multiprecision::backends::digit_base_2, void, int, 0, 0>, boost::multiprecision::et_off> (*)(const boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<1000, boost::multiprecision::backends::digit_base_2, void, int, 0, 0>, boost::multiprecision::et_off> &, const boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<1000, boost::multiprecision::backends::digit_base_2, void, int, 0, 0>, boost::multiprecision::et_off> &)>' requested here  
         data.insert(&tgamma_ratio2, arg1, arg2);  
              ^  
../../../boost/multiprecision/number.hpp:884:25: note: candidate function  
   BOOST_MP_FORCEINLINE operator unmentionable_type() const  
```  
  
What am I doing wrong? Here is my compiler info if it helps:  
  
```  
$ clang++ --version  
Apple clang version 12.0.0 (clang-1200.0.22.19)  
Target: x86_64-apple-darwin19.5.0  
Thread model: posix  
```  
  
Thanks

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-07-05 08:11:46 UTC  
> Url: https://github.com/boostorg/math/issues/391#issuecomment-653856351  

I'll look into it, those are sort of archived for future reference and probably haven't been built in a while :(

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-07-05 10:44:29 UTC  
> Url: https://github.com/boostorg/math/issues/391#issuecomment-653872319  

Issue confirmed, working on it....

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-07-05 20:28:54 UTC  
> Url: https://github.com/boostorg/math/issues/391#issuecomment-653935279  

See https://github.com/boostorg/math/pull/392.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-07-09 17:39:15 UTC  
> Url: https://github.com/boostorg/math/issues/391#issuecomment-656259979  

Fixed in develop I hope now!

---

## Comment 5

> Username: evanmiller  
> Created at: 2020-07-09 17:50:20 UTC  
> Url: https://github.com/boostorg/math/issues/391#issuecomment-656265270  

I now get the following compiler error:  
  
```  
clang-darwin.compile.c++ ../../../bin.v2/libs/math/tools/clang-darwin-12.0/debug/link-static/threading-multi/visibility-hidden/hyp_1f1_data.o  
hyp_1f1_data.cpp:9:10: fatal error: 'boost/math/special_functions/hypergeometric_1f1.hpp' file not found  
#include <boost/math/special_functions/hypergeometric_1f1.hpp>  
         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
```  
  
Are you on a case-insensitive file system?

---

## Comment 6

> Username: jzmaddock  
> Created at: 2020-07-09 18:07:47 UTC  
> Url: https://github.com/boostorg/math/issues/391#issuecomment-656273368  

Yes.  But the CI system should really have spotted that :(  
  
Will fix shortly.
