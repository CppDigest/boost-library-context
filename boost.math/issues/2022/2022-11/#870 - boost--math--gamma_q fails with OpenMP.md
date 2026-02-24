# #870 - boost::math::gamma_q fails with OpenMP [Open]

> Username: yurivict  
> Created at: 2022-11-11 02:31:31 UTC  
> Updated at: 2022-11-26 18:26:25 UTC  
> Url: https://github.com/boostorg/math/issues/870  

Testcase: [boost.cpp.txt](https://github.com/boostorg/boost/files/9985998/boost.cpp.txt)  
  
It computes values of ```boost::math::gamma_q``` wrong when run in parallel with OpenMP:  
```  
$ c++  -I /usr/local/include/ boost.cpp -std=c++17 -fopenmp  
$ ./a.out   
ncpu=0  
fail@200: 0.603301!=0.596038  
```  
  
boost-libs-1.80.0_1  
OS: FreeBSD 13.1

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-11-12 10:12:25 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1312445959  

I believe this is to be expected, I modified your program to print out epsilon differences as follows:  
  
```  
#include <vector>  
#include <iostream>  
#include <boost/math/special_functions.hpp>  
#include <omp.h>  
  
int main(int, char* [])  
{  
   omp_set_num_threads(4);  
   std::cout << "ncpu=" << omp_get_num_threads() << std::endl;  
   int size = 1000;  
   std::vector<double> x(size);  
   std::vector<double> v1(size);  
   std::vector<double> v2(size);  
   for (int j = 0; j < size; j++)  
      x[j] = 0.025 + (0.26 * j) / size;  
  
  
   auto fn = [&x](double xx) {  
      return boost::math::gamma_q(0.5 * 15.0, 0.5 / xx);  
   };  
  
   // sequential  
   for (int j = 0; j < size; j++)  
      v1[j] = fn(x[j]);  
  
   // parallel  
#pragma omp parallel for  
   for (int j = 0; j < size; j++)  
      v2[j] = fn(x[j]);  
  
   //for (int j=0; j<size; j++)  
   //  std::cout <<std::fixed<< "j="<<j+1000<<" x="<<x[j] << " v1="<<v1[j] << " v2=" << v2[j]<<std::endl;  
  
   for (int j = 0; j < size; j++)  
      if (v1[j] != v2[j])  
      {  
         std::cout << "fail@" << j << ": " << std::setprecision(18) << v1[j] << "!=" << v2[j] << " eps=" << boost::math::epsilon_difference(v1[j], v2[j]) << std::endl;  
      }  
  
   return 0;  
}  
```  
  
And the results from OpenMP and serial code are all within an epsilon or so of each other (tested on cygwin/gcc).  But here's the thing, the parallel code may generate different byte code for the calculation, see https://www.intel.com/content/dam/develop/external/us/en/documents/fp-consistency-102511-326704.pdf leading to slightly different results.  This is "normal" for floating point code.

---

## Comment 2

> Username: jschueller  
> Created at: 2022-11-13 10:15:29 UTC  
> Updated at: 2022-11-13 10:36:16 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1312694166  

the thing is I dont get any difference between the serial and omp code here on linux (gcc 12.2)  
what would explain this diffs on cygwin or freebsd you guys get ?

---

## Comment 3

> Username: yurivict  
> Created at: 2022-11-13 10:51:32 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1312700752  

@jzmaddock  
  
> And the results from OpenMP and serial code are all within an epsilon or so of each other [...]  
  
Your program prints lines like:  
```  
fail@821: 0.997031341340352428!=-24901.9137815888753 eps=1.79769313486231571e+308  
```

---

## Comment 4

> Username: jzmaddock  
> Created at: 2022-11-13 19:18:25 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1312801477  

>the thing is I dont get any difference between the serial and omp code here on linux (gcc 12.2)  
what would explain this diffs on cygwin or freebsd you guys get ?  
  
Confirmed on Godbolt: all values are identical on x64 Linux.  Sorry I have no explanation.  
  
>Your program prints lines like:  
  
>fail@821: 0.997031341340352428!=-24901.9137815888753 eps=1.79769313486231571e+308  
  
OK, that is worrying.  Looks like a garbage value has crept in there somehow.  However, checking through the code I see nothing that looks thread unsafe, it's all pretty simple stuff to be honest.  Are you able to enable thread-sanitizers to gain a clue what might be going on?  Some of the other sanitizers might help too.

---

## Comment 5

> Username: yurivict  
> Created at: 2022-11-13 22:27:06 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1312838463  

The address sanitizer option doesn't show where the problem is.  
The problem doesn't occur under valgrind.  
I've ran out of options.

---

## Comment 6

> Username: NAThompson  
> Created at: 2022-11-14 00:27:26 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1312877081  

@yurivict : `-fsanitize=thread`?

---

## Comment 7

> Username: yurivict  
> Created at: 2022-11-14 00:34:03 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1312884605  

```-fsanitize=thread``` reports:  
```  
==================                                                                                                                                                                     [33/60]  
WARNING: ThreadSanitizer: data race (pid=31824)  
  Atomic read of size 1 at 0x7b6800001740 by main thread:  
    #0 pthread_mutex_lock /disk-samsung/freebsd-src/contrib/llvm-project/compiler-rt/lib/sanitizer_common/sanitizer_common_interceptors.inc:4324:3 (a.out+0x295d36)  
    #1 __kmp_lock_suspend_mx /disk-samsung/freebsd-src/contrib/llvm-project/openmp/runtime/src/z_Linux_util.cpp:1381:16 (libomp.so+0xcd206)  
    #2 void __kmp_resume_template<kmp_flag_64<false, true> >(int, kmp_flag_64<false, true>*) /disk-samsung/freebsd-src/contrib/llvm-project/openmp/runtime/src/z_Linux_util.cpp:1594:3 (libomp  
.so+0xcd206)  
  
  Previous write of size 1 at 0x7b6800001740 by thread T2:  
    #0 _pthread_mutex_init /disk-samsung/freebsd-src/contrib/llvm-project/compiler-rt/lib/tsan/rtl/tsan_interceptors_posix.cpp:1316:3 (a.out+0x2810cb)  
    #1 __kmp_suspend_initialize_thread /disk-samsung/freebsd-src/contrib/llvm-project/openmp/runtime/src/z_Linux_util.cpp:1348:14 (libomp.so+0xcbc27)  
  
  Location is heap block of size 1504 at 0x7b6800001200 allocated by main thread:  
    #0 malloc /disk-samsung/freebsd-src/contrib/llvm-project/compiler-rt/lib/tsan/rtl/tsan_interceptors_posix.cpp:667:5 (a.out+0x27e96e)  
    #1 ___kmp_allocate_align(unsigned long, unsigned long) /disk-samsung/freebsd-src/contrib/llvm-project/openmp/runtime/src/kmp_alloc.cpp:1882:25 (libomp.so+0x54d3c)  
    #2 ___kmp_allocate /disk-samsung/freebsd-src/contrib/llvm-project/openmp/runtime/src/kmp_alloc.cpp:1934:9 (libomp.so+0x54d3c)  
  
  Thread T2 (tid=286891, running) created by main thread at:  
    #0 pthread_create /disk-samsung/freebsd-src/contrib/llvm-project/compiler-rt/lib/tsan/rtl/tsan_interceptors_posix.cpp:1022:3 (a.out+0x27ff15)  
    #1 __kmp_create_worker /disk-samsung/freebsd-src/contrib/llvm-project/openmp/runtime/src/z_Linux_util.cpp:803:7 (libomp.so+0xca8d8)  
  
SUMMARY: ThreadSanitizer: data race /disk-samsung/freebsd-src/contrib/llvm-project/openmp/runtime/src/z_Linux_util.cpp:1381:16 in __kmp_lock_suspend_mx  
==================  
```

---

## Comment 8

> Username: jschueller  
> Created at: 2022-11-14 09:34:38 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1313373743  

maybe the issue should be reopened ?

---

## Comment 9

> Username: jzmaddock  
> Created at: 2022-11-14 13:13:25 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1313677544  

I'm happy to reopen, but someone is going to have to give us clue here as the thread sanitizer messages are all from OpenMP's own runtime.  If I get time later I'll try and extract a few fragments of the incomplete gamma and perhaps someone can see if any of those still trigger the issue?

---

## Comment 10

> Username: jschueller  
> Created at: 2022-11-14 13:16:51 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1313682525  

I'd be happy to test, at least on linux.

---

## Comment 11

> Username: jzmaddock  
> Created at: 2022-11-14 18:33:55 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1314205315  

OK, here's a revised test case that basically just splits the gamma_q calculation into it's two parts, and concentrates the test values around the #821 test value identified above.  Interestingly I see identical results now for both parts on Cygwin, which is strange, but possibly suggests that wherever the cause of differences are, it's something before the functions internals are reached.  
  
But in any case, it's really OpenBSD that needs testing I guess as Linux and Cygwin look OK.  
  
```  
#include <vector>  
#include <iostream>  
#include <boost/math/special_functions.hpp>  
#include <omp.h>  
  
int main(int, char* [])  
{  
   omp_set_num_threads(4);  
   std::cout << "ncpu=" << omp_get_num_threads() << std::endl;  
   int size = 1000;  
   std::vector<double> x(size);  
   std::vector<double> v1(size);  
   std::vector<double> v2(size);  
   for (int j = 0; j < size; j++)  
      x[j] = 2.0967877212111046 - 0.1 + (0.2 * j) / size;  
  
   auto fn = [&x](double xx) {  
#if TEST == 1  
      typedef boost::math::policies::policy<> policy;  
      typedef typename boost::math::lanczos::lanczos<double, policy>::type lanczos_type;  
      double init_value = -442.75036483394916;  
      return boost::math::detail::lower_gamma_series(7.5, xx, policy(), init_value);  
#elif TEST == 2  
      typedef boost::math::policies::policy<> policy;  
      typedef typename boost::math::lanczos::lanczos<double, policy>::type lanczos_type;  
      return boost::math::detail::regularised_gamma_prefix(7.5, xx, policy(), lanczos_type());  
#else  
#error "Build with TEST=1 or TEST=2"  
#endif  
   };  
  
   // sequential  
   for (int j = 0; j < size; j++)  
      v1[j] = fn(x[j]);  
  
   // parallel  
#pragma omp parallel for  
   for (int j = 0; j < size; j++)  
      v2[j] = fn(x[j]);  
  
   //for (int j=0; j<size; j++)  
   //  std::cout <<std::fixed<< "j="<<j+1000<<" x="<<x[j] << " v1="<<v1[j] << " v2=" << v2[j]<<std::endl;  
  
   for (int j = 0; j < size; j++)  
      if (v1[j] != v2[j])  
      {  
         std::cout << "fail@" << j << ": " << std::setprecision(18) << v1[j] << "!=" << v2[j] << " eps=" << boost::math::epsilon_difference(v1[j], v2[j]) << std::endl;  
      }  
  
   return 0;  
}  
```

---

## Comment 12

> Username: jschueller  
> Created at: 2022-11-14 18:54:38 UTC  
> Updated at: 2022-11-14 18:55:17 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1314226563  

ok on linux for both 1 & 2, @yurivict can you tests this on freebsd ?

---

## Comment 13

> Username: yurivict  
> Created at: 2022-11-14 18:59:35 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1314231750  

Thread sanitizer produces same errors for both TEST=1 and TEST=2.

---

## Comment 14

> Username: yurivict  
> Created at: 2022-11-14 19:00:35 UTC  
> Updated at: 2022-11-14 19:25:55 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1314232762  

No differences are printed for both TEST=1 and TEST=2.

---

## Comment 15

> Username: yurivict  
> Created at: 2022-11-14 19:09:26 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1314245408  

Sanitizer errors seem to indicate a bug in FreeBSD libraries: https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=267768

---

## Comment 16

> Username: jzmaddock  
> Created at: 2022-11-15 18:52:48 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1315730446  

OK folks, I have another set of tests for you, @yurivict can you test on BSD?  
  
In particular I'm seeing minor differences in results when TEST=5 or 6, TEST=6 is a particularly interesting case because the code is so trivial, and there's certainly nothing non-thread safe there (even in C++03, and definitely not in C++11).  None the less the results on Cygwin are within expected tolerances.  
  
```  
#include <vector>  
#include <iostream>  
#include <boost/math/special_functions.hpp>  
#include <omp.h>  
  
double mini_erf(double z)  
{  
   using namespace boost::math;  
   using T = long double;  
  
   static const T Y = 0.405935764312744140625f;  
   static const T P[] = {  
      -0.098090592216281240205,  
      0.178114665841120341155,  
      0.191003695796775433986,  
      0.0888900368967884466578,  
      0.0195049001251218801359,  
      0.00180424538297014223957,  
   };  
   static const T Q[] = {  
      1.0,  
      1.84759070983002217845,  
      1.42628004845511324508,  
      0.578052804889902404909,  
      0.12385097467900864233,  
      0.011338523357700141101,  
      0.337511472483094676155e-5,  
   };  
   long double result = Y + tools::evaluate_polynomial(P, T(z - 0.5)) / tools::evaluate_polynomial(Q, T(z - 0.5));  
   return result;  
}  
  
int main(int, char* [])  
{  
   omp_set_num_threads(4);  
   std::cout << "ncpu=" << omp_get_num_threads() << std::endl;  
   int size = 1000;  
   std::vector<double> x(size);  
   std::vector<double> v1(size);  
   std::vector<double> v2(size);  
   for (int j = 0; j < size; j++)  
      x[j] = 0.5 / (0.025 + (0.26 * j) / size);  
  
   auto fn = [&x](double xx) {  
#if TEST == 1  
      return boost::math::gamma_q(7.5, xx);  
#elif TEST == 2  
      typedef boost::math::policies::policy<> policy;  
      typedef typename boost::math::lanczos::lanczos<double, policy>::type lanczos_type;  
      double init_value = 0;  
      return boost::math::detail::finite_half_gamma_q(7.5, xx, static_cast<double*>(nullptr), policy());  
#elif TEST == 3  
      typedef boost::math::policies::policy<> policy;  
      typedef typename boost::math::lanczos::lanczos<double, policy>::type lanczos_type;  
      double init_value = 0;  
      return boost::math::detail::lower_gamma_series(7.5, xx, policy(), 0.0);  
#elif TEST == 4  
      typedef boost::math::policies::policy<> policy;  
      typedef typename boost::math::lanczos::lanczos<double, policy>::type lanczos_type;  
      return boost::math::detail::regularised_gamma_prefix(7.5, xx, policy(), lanczos_type());  
#elif TEST == 5  
      return boost::math::erfc(std::sqrt(xx));  
#elif TEST == 6  
      return mini_erf(xx);  
#else  
#error "Build with TEST=1 or TEST=2"  
#endif  
   };  
  
   // sequential  
   for (int j = 0; j < size; j++)  
      v1[j] = fn(x[j]);  
  
   // parallel  
#pragma omp parallel for  
   for (int j = 0; j < size; j++)  
      v2[j] = fn(x[j]);  
  
   //for (int j=0; j<size; j++)  
   //  std::cout <<std::fixed<< "j="<<j+1000<<" x="<<x[j] << " v1="<<v1[j] << " v2=" << v2[j]<<std::endl;  
  
   for (int j = 0; j < size; j++)  
      if (v1[j] != v2[j])  
      {  
         std::cout << "fail@" << j << ": " << std::setprecision(18) << v1[j] << "!=" << v2[j] << " eps=" << boost::math::epsilon_difference(v1[j], v2[j]) << std::endl;  
      }  
  
   return 0;  
}  
```

---

## Comment 17

> Username: jzmaddock  
> Created at: 2022-11-24 18:19:39 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1326755651  

@yurivict Ping?

---

## Comment 18

> Username: yurivict  
> Created at: 2022-11-24 18:38:06 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1326765391  

The above program doesn't print any "fail" lines:  
```  
[yuri@yv /tmp]$ c++ -O3 -std=c++20 -D TEST=5 x.cpp -I /usr/local/include/ -fopenmp  
[yuri@yv /tmp]$ ./a.out   
ncpu=1  
[yuri@yv /tmp]$ c++ -O3 -std=c++20 -D TEST=6 x.cpp -I /usr/local/include/ -fopenmp  
[yuri@yv /tmp]$ ./a.out   
ncpu=1  
```

---

## Comment 19

> Username: jzmaddock  
> Created at: 2022-11-26 18:26:25 UTC  
> Url: https://github.com/boostorg/math/issues/870#issuecomment-1328093132  

What about with TEST=2 or 3?
