# #346 - lltrunc, llround: truncation in implicit conversion [Closed]

> Username: Kojoley  
> Created at: 2020-04-20 20:46:59 UTC  
> Updated at: 2023-04-05 15:17:20 UTC  
> Closed at: 2023-04-05 15:17:20 UTC  
> Url: https://github.com/boostorg/math/issues/346  

Running Spirit tests that use real_concept with clang-cl yields:  
  
```cpp  
In file included from C:\projects\boost\libs\spirit\test\karma\real2.cpp:7:  
In file included from C:\projects\boost\libs\spirit\test\karma/real.hpp:15:  
In file included from .\boost/math/concepts/real_concept.hpp:28:  
.\boost/math/special_functions/round.hpp(118,12): error: implicit conversion from 'long long' to 'long double' changes value from 9223372036854775807 to 9223372036854775808 [-Werror,-Wimplicit-int-float-conversion]  
   if((r > (std::numeric_limits<boost::long_long_type>::max)()) || (r < (std::numeric_limits<boost::long_long_type>::min)()))  
         ~ ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
.\boost/math/concepts/real_concept.hpp(297,23): note: in instantiation of function template specialization 'boost::math::llround<long double, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >' requested here  
{ return boost::math::llround(v.value(), policies::policy<>()); }  
                      ^  
In file included from C:\projects\boost\libs\spirit\test\karma\real2.cpp:7:  
In file included from C:\projects\boost\libs\spirit\test\karma/real.hpp:15:  
In file included from .\boost/math/concepts/real_concept.hpp:29:  
.\boost/math/special_functions/trunc.hpp(99,12): error: implicit conversion from 'long long' to 'long double' changes value from 9223372036854775807 to 9223372036854775808 [-Werror,-Wimplicit-int-float-conversion]  
   if((r > (std::numeric_limits<boost::long_long_type>::max)()) || (r < (std::numeric_limits<boost::long_long_type>::min)()))  
         ~ ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
.\boost/math/concepts/real_concept.hpp(316,23): note: in instantiation of function template specialization 'boost::math::lltrunc<long double, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >' requested here  
{ return boost::math::lltrunc(v.value(), policies::policy<>()); }  
                      ^  
```

---

## Comment 1

> Username: evanmiller  
> Created at: 2020-12-28 17:37:16 UTC  
> Url: https://github.com/boostorg/math/issues/346#issuecomment-751801503  

Possible duplicate of #430 and possibly fixed by #474
