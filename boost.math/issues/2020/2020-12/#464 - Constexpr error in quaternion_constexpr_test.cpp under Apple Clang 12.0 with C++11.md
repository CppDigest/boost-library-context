# #464 - Constexpr error in quaternion_constexpr_test.cpp under Apple Clang 12.0 with C++11 [Closed]

> Username: evanmiller  
> Created at: 2020-12-23 00:26:55 UTC  
> Updated at: 2020-12-30 20:40:17 UTC  
> Closed at: 2020-12-30 20:40:17 UTC  
> Url: https://github.com/boostorg/math/issues/464  

I came across this error in the "misc" suite running clang provided by Xcode 12.2 with `-std=c++11`. Using the same compiler, the error does not occur with C++14. Full log here:  
  
https://github.com/evanmiller/math/runs/1597836955  
  
```  
clang-darwin.compile.c++ ../../../bin.v2/libs/math/test/quaternion_constexpr_test.test/clang-darwin-12.0/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/quaternion_constexpr_test.o  
quaternion_constexpr_test.cpp:94:17: error: constexpr variable 'q12' must be initialized by a constant expression  
   constexpr qt q12 = c2 + q1;  
                ^     ~~~~~~~  
../../../boost/math/quaternion.hpp:470:46: note: non-constexpr function 'real<double>' cannot be used in a constant expression  
   return quaternion<T1>(b.R_component_1() + real(a), b.R_component_2() + imag(a), b.R_component_3(), b.R_component_4());  
                                             ^  
quaternion_constexpr_test.cpp:94:26: note: in call to 'operator+(c2, q1)'  
   constexpr qt q12 = c2 + q1;  
                         ^  
/Applications/Xcode_12.2.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/complex:821:1: note: declared here  
real(const complex<_Tp>& __c)  
^  
quaternion_constexpr_test.cpp:95:17: error: constexpr variable 'q13' must be initialized by a constant expression  
   constexpr qt q13 = q1 + c2;  
                ^     ~~~~~~~  
../../../boost/math/quaternion.hpp:464:46: note: non-constexpr function 'real<double>' cannot be used in a constant expression  
   return quaternion<T1>(a.R_component_1() + std::real(b), a.R_component_2() + std::imag(b), a.R_component_3(), a.R_component_4());  
                                             ^  
quaternion_constexpr_test.cpp:95:26: note: in call to 'operator+(q1, c2)'  
   constexpr qt q13 = q1 + c2;  
                         ^  
/Applications/Xcode_12.2.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/complex:821:1: note: declared here  
real(const complex<_Tp>& __c)  
^  
quaternion_constexpr_test.cpp:100:17: error: constexpr variable 'q17' must be initialized by a constant expression  
   constexpr qt q17 = c2 - q1;  
                ^     ~~~~~~~  
../../../boost/math/quaternion.hpp:500:26: note: non-constexpr function 'real<double>' cannot be used in a constant expression  
   return quaternion<T1>(real(a) - b.R_component_1(), imag(a) - b.R_component_2(), -b.R_component_3(), -b.R_component_4());  
                         ^  
quaternion_constexpr_test.cpp:100:26: note: in call to 'operator-(c2, q1)'  
   constexpr qt q17 = c2 - q1;  
                         ^  
/Applications/Xcode_12.2.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/complex:821:1: note: declared here  
real(const complex<_Tp>& __c)  
^  
quaternion_constexpr_test.cpp:101:17: error: constexpr variable 'q18' must be initialized by a constant expression  
   constexpr qt q18 = q1 - c2;  
                ^     ~~~~~~~  
../../../boost/math/quaternion.hpp:494:46: note: non-constexpr function 'real<double>' cannot be used in a constant expression  
   return quaternion<T1>(a.R_component_1() - std::real(b), a.R_component_2() - std::imag(b), a.R_component_3(), a.R_component_4());  
                                             ^  
quaternion_constexpr_test.cpp:101:26: note: in call to 'operator-(q1, c2)'  
   constexpr qt q18 = q1 - c2;  
                         ^  
/Applications/Xcode_12.2.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/complex:821:1: note: declared here  
real(const complex<_Tp>& __c)  
^  
4 errors generated.  
```

---

## Comment 1

> Username: mborland  
> Created at: 2020-12-24 06:37:54 UTC  
> Url: https://github.com/boostorg/math/issues/464#issuecomment-750771168  

`std::real()` and `std::imag()` did not become constexpr until C++14. I will have a fix in shortly.

---

## Comment 2

> Username: evanmiller  
> Created at: 2020-12-30 20:40:17 UTC  
> Url: https://github.com/boostorg/math/issues/464#issuecomment-752751545  

I can confirm this issue is fixed in #470. Closing.
