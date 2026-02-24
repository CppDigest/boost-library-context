# #688 - Tests done on Intel/AVX&AVX2 -march=native should fail [Closed]

> Username: trex58  
> Created at: 2020-03-26 15:20:24 UTC  
> Updated at: 2020-03-26 16:28:21 UTC  
> Closed at: 2020-03-26 16:28:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/688  

Hi,  
I am porting Boost 1.69 to AIX.  
I have issues with -O2 due to the Power **fmadd** assembler instruction being used with -O2 and thus generating NOT PERFECT results.  
See: https://github.com/boostorg/geometry/issues/686  for my issue on AIX, dealing with a test of "geometry".  
I explain in this 686 issue that we see the same kind of issue with Intel **vfmadd132sd** assembler instruction, on a small non-Boost example.  
  
Now, when running a math test on Intel HW with **AVX & AVX2** features, and when using **-march=native** , I see the same issue on Intel that I have on AIX.  
(for now, we have GCC 7.5 only. Will move to GCC 8.4 asap)  
  
With math test:  
  ../bin.v2/libs/math/test/test_lambert_w.test/gcc-7.5.0/debug/link-static/visibility-hidden/**test_lambert_w**.run…  
we have:  
  
**AIX error message:**  
```  
    ../libs/math/test/test_lambert_w.cpp(1034): error:  
         in "test_range_of_double_values":  
         difference{2.61848e-09}  
         between  
            lambert_w0(create_test_value<RealType>( -0.36787944117144228L, "-0.36787944117144228",  
            boost::mpl::bool_< std::numeric_limits<RealType>::is_specialized && (std::numeric_limits<RealType>::radix == 2)  
            && (std::numeric_limits<RealType>::digits <= std::numeric_limits<long double>::digits)  
            && boost::is_convertible<largest_float, RealType>::value>(),  
            boost::mpl::bool_< boost::is_constructible<RealType, const char*>::value>())){-0.99999998154505632}  
         and  
                       create_test_value<RealType>( -0.99999997892657588L, "-0.99999997892657588",  
            boost::mpl::bool_< std::numeric_limits<RealType>::is_specialized && (std::numeric_limits<RealType>::radix == 2)  
            && (std::numeric_limits<RealType>::digits <= std::numeric_limits<long double>::digits)  
            && boost::is_convertible<largest_float, RealType>::value>(),  
            boost::mpl::bool_< boost::is_constructible<RealType, const char*>::value>()) {-0.99999997892657588}  
         exceeds  
                 2.2204460492503131e-16  
```  
  
**Intel error message:**  
```  
Test Lambert W function type double for range of values.  
Tolerance 1 * epsilon == 2.22045e-16  
../libs/math/test/test_lambert_w.cpp(1034): error: in "test_range_of_double_values":  
  difference{2.61848e-09}  
  between  
        lambert_w0(create_test_value <RealType>( -0.36787944117144228Q, "-0.36787944117144228",  
     boost::mpl::bool_< std::numeric_limits<RealType>::is_specialized &&  
     (std::numeric_limits<RealType>::radix == 2) && (std::numeric_limits<RealType>::digits <= 113)  
     && boost::is_convertible<largest_float, RealType>::value>(),  
     boost::mpl::bool_< boost::is_constructible<RealType, const char*>::value>())){-0.99999998154505632}  
   and  
                    create_test_value<RealType>( -0.99999997892657588Q, "-0.99999997892657588",  
     boost::mpl::bool_< std::numeric_limits<RealType>::is_specialized &&  
     (std::numeric_limits<RealType>::radix == 2) && (std::numeric_limits<RealType>::digits <= 113)  
     && boost::is_convertible<largest_float, RealType>::value>(),  
     boost::mpl::bool_< boost::is_constructible<RealType, const char*>::value>()){-0.99999997892657588}  
   exceeds  
              2.2204460492503131e-16  
```  
  
Note how close are the 2 error messages.  
  
On Intel, we compiled with:  
```  
gcc.compile.c++ ../bin.v2/libs/math/test/test_lambert_w.test/gcc-7.5.0/debug/link-static/visibility-hidden/test_lambert_w.o  
  
    "g++"  -march=native -fvisibility-inlines-hidden -m64 -g -fvisibility=hidden -O2 -g -m64 -fmessage-length=0 -D_FORTIFY_SOURCE=2 -fstack-protector -funwind-tables -fasynchronous-unwind-tables -fno-strict-aliasing -Wno-unused-local-typedefs -Wno-deprecated-declarations  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_CHRONO_THREAD_DISABLED -DBOOST_HAS_ICU=1 -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_TIMER_STATIC_LINK=1 -DBOOST_UBLAS_UNSUPPORTED_COMPILER=0  -I".." -I"../libs/math/include_private" -I"../libs/math/test" -I"/usr/include" -c -o "../bin.v2/libs/math/test/test_lambert_w.test/gcc-7.5.0/debug/link-static/visibility-hidden/test_lambert_w.o" "../libs/math/test/test_lambert_w.cpp"  
  
```  
We are now rebuilding Boost and retesting the "math" module on same Intel HW after adding the **-ffp-contract=off** option, in order to NOT use **fmadd** instruction. And I'll be able to provide the result soon. It should show that the above issue disappears.  
  
As a **conclusion**, I think that it would be worth to use the GCC -march=native option on INTEL hardware having AVX & AVX2 features, in order to see how much tests are broken by -O2 and thus the fmadd instruction.

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-03-26 16:19:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/688#issuecomment-604525920  

Boost.Math is a different library. You should've reported it here: https://github.com/boostorg/math

---

## Comment 2

> Username: trex58  
> Created at: 2020-03-26 16:26:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/688#issuecomment-604529686  

Oh yes. I'm new to Boost. Sorry.  
However, this issue with **-O2 / fmadd** deals with all boost libraries. Just providing this here within geometry since it's the next step of my analysis.  
Anyway, I'll open it on math.  
Sorry for the inconvenience.  
Please notice that I have added a new comment in https://github.com/boostorg/geometry/issues/686 about test algorithms_densify showing the issue.

---

## Comment 3

> Username: trex58  
> Created at: 2020-03-26 16:28:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/688#issuecomment-604530683  

Issue moved to: https://github.com/boostorg/math/issues/327
