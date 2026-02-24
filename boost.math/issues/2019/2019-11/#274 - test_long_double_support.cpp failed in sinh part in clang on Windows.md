# #274 - test_long_double_support.cpp failed in sinh part in clang on Windows [Closed]

> Username: yuxianch  
> Created at: 2019-11-28 06:44:30 UTC  
> Updated at: 2024-02-22 10:32:02 UTC  
> Closed at: 2024-02-22 10:32:02 UTC  
> Url: https://github.com/boostorg/math/issues/274  

I was using clang-cl on Windows to run the tests from math module, and found [test_long_double_support.cpp](https://github.com/boostorg/math/blob/develop/test/test_long_double_support.cpp) failed in sinh part.  
This is what I got when executing this test.  
```  
...  
std::sinh<long double> Max = 0 RMS Mean=0  
  
std::asinh<long double> Max = 0 RMS Mean=0  
  
::sinhl<long double> Max = 0 RMS Mean=0  
  
unknown location(0): fatal error: in "test_main": memory access violation occurred at address 0x00000001, while attempting to  read inaccessible data  
test_long_double_support.cpp(348): last checkpoint: "test_main" test entry  
  
*** 1 failure is detected in the test module "Master Test Suite"  
  
EXIT STATUS: 201  
```  
In the same time, cl can successfully run this test.   
Could someone explain the reason?  
If you need more information, please let me know.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-11-28 09:49:02 UTC  
> Url: https://github.com/boostorg/math/issues/274#issuecomment-559421564  

That test doesn't include any of Boost.Math:  it's there to verify that the C++ std lib is functional for type `long double`.  If you can work out which call is responsible, you should be able to reproduce without any of Boost.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-11-28 10:12:49 UTC  
> Url: https://github.com/boostorg/math/issues/274#issuecomment-559430555  

I tried to reproduce with:  
  
clang-cl /GS /W3  /Gd /MDd /EHsc -DBOOST_ALL_NO_LIB -I../../.. -I../include_private test_long_double*.cpp <other sources>  
  
and it built and ran just fine.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-11-28 10:13:30 UTC  
> Url: https://github.com/boostorg/math/issues/274#issuecomment-559430797  

Oops, that command line was supposed to indicate that there were other source files after the one given :(
