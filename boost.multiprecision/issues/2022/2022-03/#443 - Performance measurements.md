# #443 - Performance measurements [Closed]

> Username: kaba2  
> Created at: 2022-03-21 20:20:17 UTC  
> Updated at: 2022-06-11 12:51:36 UTC  
> Closed at: 2022-03-23 00:58:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/443  

The documentation states that `cpp_bin_float` is about 2x slower than `mpf_float` and `mpfr_float`. I wrote the following test:  
  
`testmp.cpp`:  
  
```cpp  
#include <iostream>  
#include <chrono>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/mpfr.hpp>  
#include <boost/multiprecision/gmp.hpp>  
  
using namespace boost::multiprecision;  
using mpf_float_15 = number<gmp_float<15>>;  
  
using namespace boost::multiprecision;  
using mpfr_float_15 = number<mpfr_float_backend<15>>;  
  
template<  
    class TimeT  = std::chrono::milliseconds,  
    class ClockT = std::chrono::steady_clock,  
    class F>  
static void duration(F&& func)  
{  
    auto start = ClockT::now();  
    auto x = func();  
    auto delta = std::chrono::duration_cast<TimeT>(ClockT::now() - start);  
    std::cout.precision(15);  
    std::cout << delta << " (" << x << ")" << std::endl;  
}  
  
template <typename T>  
void mySqrt(const T& a, T& x) {  
   const long long n = 10'000'000;  
   for (long long i = 0;i < n; ++i) {  
      x = (x / a + x) / 2;  
   }  
}  
  
double testDouble() {  
   const double a = 2;  
   double x = 1;  
   mySqrt(a, x);  
   return x;  
}  
  
cpp_bin_float_double testCpp() {  
   const cpp_bin_float_double a = 2;  
   cpp_bin_float_double x = 1;  
   mySqrt(a, x);  
   return x;  
}  
  
mpfr_float_15 testMpfr() {  
   //mpfr_float::default_precision(15);  
   const mpfr_float_15 a = 2;  
   mpfr_float_15 x = 1;  
   mySqrt(a, x);  
   return x;  
}  
  
mpf_float_15 testGmp() {  
   //mpf_float::default_precision(15);  
   const mpf_float_15 a = 2;  
   mpf_float_15 x = 1;  
   mySqrt(a, x);  
   return x;  
}  
  
int main()  
{  
   duration(testDouble);  
   duration(testCpp);  
   duration(testMpfr);  
   duration(testGmp);  
}  
```  
  
Results:  
```  
31ms (9.88131291682493e-324)  
151ms (0)  
1428ms (4.30444338913404e-1249388)  
1466ms (4.30444338913269e-1249388)  
```  
  
Here `cpp_bin_float` is about 9x faster than `mpfr_float`or `mpf_float` (in Release build). What might explain this behavior?  
  
`CMakeLists.txt`:  
  
```cmake  
cmake_minimum_required(VERSION 3.15)  
  
project(testmp CXX)  
  
find_package(Boost REQUIRED)  
  
link_directories("./vcpkg/packages/gmp_x64-windows/lib")   
link_directories("./vcpkg/packages/mpfr_x64-windows/lib")   
  
add_executable(testmp testmp.cpp)  
target_compile_features(testmp PRIVATE cxx_std_20)  
  
target_link_libraries(testmp PRIVATE Boost::headers mpfr gmp)  
```  
  
`vcpkg.json`:  
  
```json  
{  
  "name": "testmp",  
  "version-string": "0.1.0",  
  "dependencies": [  
    "boost-multiprecision",  
    "mpfr"  
  ]  
}  
```  
  
Command line:  
```  
git clone https://github.com/Microsoft/vcpkg.git  
.\vcpkg\bootstrap-vcpkg.bat  
cmake -B build -S . -DCMAKE_TOOLCHAIN_FILE=./vcpkg/scripts/buildsystems/vcpkg.cmake  
cmake --build build --config Release  
```

---

## Comment 1

> Username: kaba2  
> Created at: 2022-03-22 04:53:09 UTC  
> Updated at: 2022-03-22 04:58:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/443#issuecomment-1074725061  

The function is named `mySqrt`, because I meant it to compute the square root through the Babylonian method. However, I made a typo and wrote `x = (x / a + x) / 2` instead of the correct `x = (a / x + x) / 2`. The former is equivalent to `x = 0.75 * x` which converges to zero. Perhaps that has something to do with the timings. The correct Babylonian method provides the following timings:  
  
```  
49ms (1.41421356237309)  
2267ms (1.41421356237309)  
1292ms (1.41421356237309)  
2118ms (1.4142135623731)  
```  
  
These are more in line with the documentation. Here `mpfr_float` is the fastest, but `mpf_float` is comparable to `cpp_bin_float`.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2022-03-22 07:09:13 UTC  
> Updated at: 2022-03-22 07:11:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/443#issuecomment-1074810612  

> These are more in line with the documentation. Here `mpfr_float` is the fastest, but `mpf_float` is comparable to `cpp_bin_float`...  
  
Hi @kaba2 the documentation does not really tell the whole story, but a very terse partial summary of the situation. For low digit counts, GMP and MPFR might suffer from the cost of memory allocation operations. These might be even more time costly than the operations themselves. `cpp_bin_float` uses stack allocation for low digit counts and _can_ actually be faster in some cases than GMP.  
  
@jzmaddock might have more to add to this detailed story. And perhaps we should add some details somewhere in the docs. Although I had a vague recollection that we covered part of this somewhere --- on the other hand, we might have covered this in offline posts and personal communications only.  
  
I prefer your modified code, which i modified a bit more to explicitly use the `count` method of the time spans and also your corrected square root algorithm.  
  
Modified code:  
  
```cpp  
#include <iostream>  
#include <chrono>  
  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <boost/multiprecision/mpfr.hpp>  
#include <boost/multiprecision/gmp.hpp>  
  
// g++ -Wall -O3 -I/mnt/c/boost/boost_1_78_0 test.cpp -lgmp -o test.exe  
  
using namespace boost::multiprecision;  
using mpf_float_15 = number<gmp_float<15>>;  
  
using namespace boost::multiprecision;  
using mpfr_float_15 = number<mpfr_float_backend<15>>;  
  
template<  
    class TimeT  = std::chrono::milliseconds,  
    class ClockT = std::chrono::steady_clock,  
    class F>  
static void duration(F&& func)  
{  
    auto start = ClockT::now();  
    auto x = func();  
    auto delta = std::chrono::duration_cast<TimeT>(ClockT::now() - start);  
    std::cout.precision(15);  
    std::cout << delta.count() << "ms (" << x << ")" << std::endl;  
}  
  
template <typename T>  
void mySqrt(const T& a, T& x) {  
   const long long n = 10'000'000;  
   for (long long i = 0;i < n; ++i) {  
      x = (a / x + x) / 2;  
   }  
}  
  
double testDouble() {  
   const double a = 2;  
   double x = 1;  
   mySqrt(a, x);  
   return x;  
}  
  
cpp_bin_float_double testCpp() {  
   const cpp_bin_float_double a = 2;  
   cpp_bin_float_double x = 1;  
   mySqrt(a, x);  
   return x;  
}  
  
mpfr_float_15 testMpfr() {  
   //mpfr_float::default_precision(15);  
   const mpfr_float_15 a = 2;  
   mpfr_float_15 x = 1;  
   mySqrt(a, x);  
   return x;  
}  
  
mpf_float_15 testGmp() {  
   //mpf_float::default_precision(15);  
   const mpf_float_15 a = 2;  
   mpf_float_15 x = 1;  
   mySqrt(a, x);  
   return x;  
}  
  
int main()  
{  
   duration(testDouble);  
   duration(testCpp);  
   duration(testMpfr);  
   duration(testGmp);  
}  
```

---

## Comment 3

> Username: ckormanyos  
> Created at: 2022-03-22 07:52:01 UTC  
> Updated at: 2022-03-22 07:53:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/443#issuecomment-1074841765  

In a next step, I actually, went on to bump up to 51 and then to 101 decimal digits and obtained the following test results:  
  
at 51 digits  
  
```sh  
94ms (1.41421356237309492343001693370752036571502685546875)  
7544ms (1.41421356237309504880168872420969807856967187537695)  
2200ms (1.41421356237309504880168872420969807856967187537695)  
2516ms (1.41421356237309504880168872420969807856967187537695)  
```  
  
at 101 digits  
  
```sh  
77ms (1.41421356237309492343001693370752036571502685546875)  
15426ms (1.4142135623730950488016887242096980785696718753769480731766797379907324784621070388503875343276415727)  
3057ms (1.4142135623730950488016887242096980785696718753769480731766797379907324784621070388503875343276415727)  
4326ms (1.4142135623730950488016887242096980785696718753769480731766797379907324784621070388503875343276415727)  
```  
  
At higher digit counts, we see the performance expectations.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2022-03-22 16:45:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/443#issuecomment-1075381579  

@ckormanyos 's results look about right to me.  
  
With regard to the original results, and noting that x quickly goes to zero in the cpp_bin_float case, we note that:  
  
x/a is 0/a and gets short-cutted to zero.  
Adding x to the result (0) gets short-cutted to a no-op.  
Dividing the result (0) by 2 gets short-cutted to a no-op.  
  
OK the functions still get called, but the main loop of the arithmetic operation is never reached.  I'm surprised the double version doesn't underflow to zero though....

---

## Comment 5

> Username: kaba2  
> Created at: 2022-03-23 00:55:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/443#issuecomment-1075797575  

Thanks for the comments and for the work on Boost.Multiprecision. It would be great to have some crude numbers in the documentation. Perhaps these here. It does not need to be comprehensive, just to have some hunch. Also a mention on how the speed compares to just using doubles. Above shows about 26x slowdown for using a comparable `mpfr_float`.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2022-03-23 07:54:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/443#issuecomment-1076038969  

In the beginning of the project and throughout, we have performed many performance analyses, counts of multiprecision-operations-per-second and the like. Some of these are already listed in the ensuing sections of the docs.  
  
> ... would be great to have some crude numbers in the documentation ...  
  
You are right. I think also there could be, in fact, a lot of doc-benefit from a two-paragraph or so terse summary of the overall performance situation when using multipreciswion types, going away from fast FPU speeds, etc.  
  
If this were to be done, a good place might be [here](https://www.boost.org/doc/libs/1_78_0/libs/multiprecision/doc/html/boost_multiprecision/perf.html) or somewhere near that place.  
  
John (@jzmaddock) do you agree? If so, I could draft up some paragraphs for that part of the docs and we could put them in for 1.80, since I do not want to disturb the (relative) peace ensuing for 1.79.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2022-03-23 16:36:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/443#issuecomment-1076549272  

Although there's not much commentary, this page gives a pretty reasonable summary of the performance differences: https://www.boost.org/doc/libs/1_78_0/libs/multiprecision/doc/html/boost_multiprecision/perf/realworld.html  ?

---

## Comment 8

> Username: ofloveandhate  
> Created at: 2022-03-23 20:02:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/443#issuecomment-1076767446  

`realworld.html` ... That page would be better both with commentary, and if it linked to the code for the tests.

---

## Comment 9

> Username: ckormanyos  
> Created at: 2022-03-24 07:37:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/443#issuecomment-1077324897  

> `realworld.html` ... That page would be better both with commentary,  
  
I can add commentary (and maybe some relevant links) to [realworld.html](https://www.boost.org/doc/libs/1_78_0/libs/multiprecision/doc/html/boost_multiprecision/perf/realworld.html) for 1.80.

---

## Comment 10

> Username: ckormanyos  
> Created at: 2022-03-24 07:40:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/443#issuecomment-1077327191  

See also #444

---

## Comment 11

> Username: ckormanyos  
> Created at: 2022-06-07 09:49:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/443#issuecomment-1148447987  

> can add commentary ... for 1.80.  
  
See also #471

---

## Comment 12

> Username: ckormanyos  
> Created at: 2022-06-11 12:51:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/443#issuecomment-1152922566  

See also #444
