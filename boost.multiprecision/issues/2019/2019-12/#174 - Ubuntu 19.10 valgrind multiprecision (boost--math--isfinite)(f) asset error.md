# #174 - Ubuntu 19.10 valgrind multiprecision (boost::math::isfinite)(f) asset error [Closed]

> Username: Bushstar  
> Created at: 2019-12-05 11:57:44 UTC  
> Updated at: 2019-12-09 14:33:15 UTC  
> Closed at: 2019-12-07 19:27:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/174  

When running the following program with valgrind on Ubuntu 19.10 I get an asset error, no error is thrown when running without valgrind or when trying to run with valgrind on Ubuntu 18.04. The version of Boost installed on 19.10 is 1.67, I've also used 1.70 and get the same result.  
  
To replicate on a new Digital Ocean Ubuntu 19.10 droplet.  
  
`sudo apt-get install -y build-essential libboost-system-dev valgrind`  
  
Create valgrind_bug_test1.cpp  
```  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
int main(int argc, char *argv[])  
{  
    boost::multiprecision::cpp_dec_float_100 x = 1.0;  
    return 0;  
}  
```  
  
Compile with  
  
`g++ -g -o valgrind_bug_test1 valgrind_bug_test1.cpp`  
  
Running with valgrind.  
```  
valgrind ./valgrind_bug_test1   
==7121== Memcheck, a memory error detector  
==7121== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.  
==7121== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info  
==7121== Command: ./valgrind_bug_test1  
==7121==   
valgrind_bug_test1: /usr/include/boost/multiprecision/cpp_dec_float.hpp:2298: boost::multiprecision::backends::cpp_dec_float<Digits10, ExponentType, Allocator>& boost::multiprecision::backends::cpp_dec_float<Digits10, ExponentType, Allocator>::operator=(long double) [with unsigned int Digits10 = 100; ExponentType = int; Allocator = void]: Assertion `(boost::math::isfinite)(f)' failed.  
==7121==   
==7121== Process terminating with default action of signal 6 (SIGABRT)  
==7121==    at 0x4BED3EB: raise (raise.c:51)  
==7121==    by 0x4BCC898: abort (abort.c:79)  
==7121==    by 0x4BCC768: __assert_fail_base.cold (assert.c:92)  
==7121==    by 0x4BDE005: __assert_fail (assert.c:101)  
==7121==    by 0x10C5C6: boost::multiprecision::backends::cpp_dec_float<100u, int, void>::operator=(long double) (cpp_dec_float.hpp:2298)  
==7121==    by 0x10C30F: boost::multiprecision::backends::cpp_dec_float<100u, int, void>::cpp_dec_float<long double>(long double, boost::enable_if_c<boost::is_floating_point<long double>::value&&(!boost::is_same<long double, __float128>::value), void>::type*) (cpp_dec_float.hpp:269)  
==7121==    by 0x1103EE: boost::multiprecision::backends::cpp_dec_float<100u, int, void>::long_double_max() (cpp_dec_float.hpp:373)  
==7121==    by 0x10E4E7: boost::multiprecision::backends::cpp_dec_float<100u, int, void>::initializer::initializer() (cpp_dec_float.hpp:168)  
==7121==    by 0x10BA4F: __static_initialization_and_destruction_0(int, int) (cpp_dec_float.hpp:639)  
==7121==    by 0x10BA69: _GLOBAL__sub_I_main (valgrind_bug_test1.cpp:7)  
==7121==    by 0x1166EC: __libc_csu_init (in /root/valgrind_bug_test1)  
==7121==    by 0x4BCE16D: (below main) (libc-start.c:264)  
==7121==   
==7121== HEAP SUMMARY:  
==7121==     in use at exit: 0 bytes in 0 blocks  
==7121==   total heap usage: 368 allocs, 368 frees, 111,878 bytes allocated  
==7121==   
==7121== All heap blocks were freed -- no leaks are possible  
==7121==   
==7121== For lists of detected and suppressed errors, rerun with: -s  
==7121== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)  
Aborted (core dumped)  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-12-05 18:19:39 UTC  
> Updated at: 2019-12-05 18:40:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/174#issuecomment-562251143  

Reproduced, the program dies before main during static data initialization.  A reduced, non-boost, test case is:  
  
```cpp  
#include <climits>  
#include <cmath>  
  
int main()  
{  
  long double f = std::numeric_limits<double>::max();  
  int e;  
  assert(std::frexp(f, &e) < 1);  // passes  
  f *= 2;  
  if(!std::isinf(f))                         // passes, f is still finite  
    assert(std::frexp(f, &e) < 1);  // fails.  
  return 0;  
}  
```  
  
I've reported as a valgrind bug here: https://bugs.kde.org/show_bug.cgi?id=414870  
  
Unfortunately at present I don't see any workarounds.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-12-06 11:09:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/174#issuecomment-562532084  

Update: the issue is introduced in GCC-9, GCC-8 is fine even on ubuntu-19.10.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-12-07 19:27:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/174#issuecomment-562879356  

I'm hoping this is now fixed in develop: the catch is that there is now some code duplication between double and long double assignment operators.  However, as long as you only use doubles in your code, so will cpp_dec_float internally, and hence the valgrind issue shouldn't arise.

---

## Comment 4

> Username: Bushstar  
> Created at: 2019-12-09 14:33:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/174#issuecomment-563265330  

Many thanks for your work on this.
