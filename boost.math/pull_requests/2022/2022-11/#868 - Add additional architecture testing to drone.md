# #868 Add additional architecture testing to drone [Merged]

> Username: mborland  
> Created at: 2022-11-05 18:31:24 UTC  
> Updated at: 2023-01-16 19:13:08 UTC  
> Merged at: 2023-01-16 19:13:04 UTC  
> Closed at: 2023-01-16 19:13:04 UTC  
> Url: https://github.com/boostorg/math/pull/868  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-11-05 18:49:45 UTC  
> Url: https://github.com/boostorg/math/pull/868#issuecomment-1304610183  

Cool, I hadn't realised we had that facility!

---

## Comment 2

> Username: mborland  
> Created_at: 2022-11-05 18:59:29 UTC  
> Url: https://github.com/boostorg/math/pull/868#issuecomment-1304611909  

@sdarwin Do you see my error in adding M1? It's mostly copy-paste from the slack channel but I am getting the error `linter: duplicate pipeline names` on [drone side.](https://drone.cpp.al/boostorg/math/978)

---

## Comment 3

> Username: sdarwin  
> Created_at: 2022-11-05 20:41:38 UTC  
> Url: https://github.com/boostorg/math/pull/868#issuecomment-1304638110  

@mborland the first argument 'job name' must be unique.  Change to `"Clang " + cxx`

---

## Comment 4

> Username: mborland  
> Created_at: 2022-11-06 00:49:07 UTC  
> Url: https://github.com/boostorg/math/pull/868#issuecomment-1304680886  

@jzmaddock This seems to be working now, but produced some errors in `special_fun` and `distribution_tests`. Most of them I think are safe to just loosen the tolerance on a hair but several feel more nefarious like this one:  
  
```  
Testing: Elliptic Integral Jacobi Zeta: Random Data  
953s  
6099	jacobi_zeta<double> Max = 2.232e+14 RMS Mean=1.537e+13  
953s  
6100	    worst case at row: 1921  
953s  
6101	    { 1.4786438941955566, 3.2146250106279695e-09, 4.7345199477425445e-19 }  
953s  
6102	Peak error greater than expected value of 1  
953s  
6103	./handle_test_result.hpp:165: error: in "test_main": check bounds.first >= max_error_found has failed  
953s  
6104	Mean error greater than expected value of 1  
953s  
6105	./handle_test_result.hpp:170: error: in "test_main": check bounds.second >= mean_error_found has failed  
```  
  
Interestingly, I can't reproduce any of the errors on my M1 machine. @NAThompson are you able to reproduce?

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2022-12-28 18:24:40 UTC  
> Url: https://github.com/boostorg/math/pull/868#issuecomment-1366839532  

@mborland : I'm going to extract the s390x logic out of this to a separate PR as that's the closest to being green, and then we should go from there.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2023-01-15 18:25:32 UTC  
> Url: https://github.com/boostorg/math/pull/868#issuecomment-1383218981  

@mborland : do we need to revisit running M1 tests?  I'm sure I'm missing something, but I'm not seeing what makes these tests run on M1?

---

## Comment 7

> Username: mborland  
> Created_at: 2023-01-15 18:36:36 UTC  
> Url: https://github.com/boostorg/math/pull/868#issuecomment-1383221008  

> @mborland : do we need to revisit running M1 tests? I'm sure I'm missing something, but I'm not seeing what makes these tests run on M1?  
  
Now that you have ARM working I think it would be easy to add. It is generally this line:  
```  
      result.append(osx_cxx("M1 Clang " + cxx + " " + suite, "clang++", buildscript="drone", buildtype="boost", xcode_version="14.1", environment={'TOOLSET': 'clang', 'CXXSTD': cxx, 'TEST_SUITE': suite, 'DEFINE': 'BOOST_MATH_NO_REAL_CONCEPT_TESTS,BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS,BOOST_MATH_MULTI_ARCH_CI_RUN', }, globalenv=globalenv))  
```  
  
The macOS machines in drone are supposed to be M1.

---

## Comment 8

> Username: sdarwin  
> Created_at: 2023-01-15 19:47:57 UTC  
> Url: https://github.com/boostorg/math/pull/868#issuecomment-1383235438  

While the macOS machines are m1, it seems the OS can simulate x86 or use the native arm architecture.     
  
The drone process appears to be using x86 so I need to work on fixing that.

---

## Comment 9

> Username: mborland  
> Created_at: 2023-01-15 19:51:48 UTC  
> Url: https://github.com/boostorg/math/pull/868#issuecomment-1383236195  

>   
  
That makes sense because I was getting failures from CI that I could not reproduce locally on my M1 machine. Thanks.

---

## Comment 10

> Username: sdarwin  
> Created_at: 2023-01-16 14:00:38 UTC  
> Url: https://github.com/boostorg/math/pull/868#issuecomment-1384102346  

Switched m1 drone to arm64. Try it again.

---

## Comment 11

> Username: mborland  
> Created_at: 2023-01-16 19:12:39 UTC  
> Url: https://github.com/boostorg/math/pull/868#issuecomment-1384457279  

@jzmaddock It looks like M1 is good to go as well now. Merging.

---
