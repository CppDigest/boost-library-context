# #82 mark compile time constants as constexpr [Closed]

> Username: lakshayg  
> Created at: 2017-08-22 06:58:15 UTC  
> Updated at: 2018-02-14 18:44:54 UTC  
> Closed at: 2018-02-14 18:44:54 UTC  
> Url: https://github.com/boostorg/math/pull/82  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-08-22 08:23:34 UTC  
> Url: https://github.com/boostorg/math/pull/82#issuecomment-323955250  

Note that those changes don't actually make the functions constexpr - or indeed change the way the constants are initialized - in fact in C++14 BOOST_STATIC_CONSTEXPR doesn't even imply the constants are "const" (C++11 is different, and early implementations prevent you from writing "constexpr const" which is what C++14 permits).  
  
We also need to update test_bernoulli_constants.cpp and test_factorials.cpp with some trivial constexpr tests to verify that the functions really can be called in constexpr contexts.

---

## Comment 2

> Username: lakshayg  
> Created_at: 2017-08-23 17:33:56 UTC  
> Url: https://github.com/boostorg/math/pull/82#issuecomment-324407458  

@jzmaddock There are some build failures on AppVeyor. I am unable to reproduce these failures on my machine. Do you know of a way this can be done?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-08-23 18:14:27 UTC  
> Url: https://github.com/boostorg/math/pull/82#issuecomment-324418583  

I can reproduce with both gcc and msvc and:  
  
cd boost-root/libs/math/test  
../../../b2 multiprc_concept_check_3  
  
for example.  The issue is that this specialization is used for multiprecision types, so the array can never be a literal type and therefore constexpr.  For example with GCC I see:  
  
```  
gcc.compile.c++ ..\..\..\bin.v2\libs\math\test\multiprc_concept_check_3.test\gcc-mingw-6.3.0s14\release\link-static\pch-off\multiprc_concept_check_3.o  
In file included from ..\..\../boost/math/special_functions/gamma.hpp:33:0,  
                 from ..\..\../boost/multiprecision/cpp_dec_float.hpp:45,  
                 from multiprc_concept_check_3.cpp:23:  
..\..\../boost/math/special_functions/detail/unchecked_factorial.hpp: In instantiation of 'T boost::math::unchecked_factorial_imp(unsigned int, const mpl_::int_<N>&) [with T = boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >; int N = 169]':  
..\..\../boost/math/special_functions/detail/unchecked_factorial.hpp:754:37:   required from 'RT boost::math::unchecked_factorial(unsigned int) [with RT = boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >]'  
compile_test/instantiate.hpp:179:46:   required from 'void instantiate(RealType) [with RealType = boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >]'  
multiprc_concept_check_3.cpp:35:27:   required from here  
..\..\../boost/math/special_functions/detail/unchecked_factorial.hpp:613:5: error: the type 'const boost::array<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u> >, 101ull>' of constexpr variable 'factorials' is not literal  
    }};  
     ^  
```  
  
Long and the short of it, is that specialization should not have it's internals marked as constexpr.  
  
HTH, John.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2018-02-14 18:44:54 UTC  
> Url: https://github.com/boostorg/math/pull/82#issuecomment-365705140  

I've implemented this slightly differently and merged to develop.

---
