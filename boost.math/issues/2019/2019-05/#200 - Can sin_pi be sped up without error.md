# #200 - Can sin_pi be sped up without error? [Closed]

> Username: NAThompson  
> Created at: 2019-05-01 15:46:45 UTC  
> Updated at: 2019-05-13 17:43:50 UTC  
> Closed at: 2019-05-13 17:37:39 UTC  
> Url: https://github.com/boostorg/math/issues/200  

I noticed that `sin_pi` seems a little slow. What I don't know is whether or not this necessary for some reason or another.  
  
I have written the following benchmarks, and also given what I think may be a simplified implementation (but not yet checked its ULP accuracy):  
  
```cpp  
#include <benchmark/benchmark.h>  
#include <random>  
#include <iostream>  
#include <algorithm>  
#include <boost/math/special_functions/sin_pi.hpp>  
  
template<class Real>  
void BM_SinPi(benchmark::State& state)  
{  
  
    std::mt19937 gen(323723);  
    std::uniform_real_distribution<Real> dis(-0.95, 0.95);  
  
    for (auto _ : state)  
    {  
        Real arg = dis(gen);  
        benchmark::DoNotOptimize(boost::math::sin_pi(arg));  
    }  
}  
BENCHMARK_TEMPLATE(BM_SinPi, float);  
BENCHMARK_TEMPLATE(BM_SinPi, double);  
  
  
template<class Real>  
Real sin_pi2(Real x)  
{  
    using std::sin;  
    using std::floor;  
    using boost::math::constants::pi;  
    if(x < 0) {  
      return -sin_pi2(-x);  
    }  
  
    Real integer_part = floor(x);  
    Real rem = x - integer_part;  
    if (rem > 0.5) {  
        rem = 1 - rem;  
    }  
  
    long i = static_cast<long>(integer_part);  
  
    if (i & 1) {  
        return -sin(pi<Real>()*rem);  
    } else {  
        return sin(pi<Real>()*rem);  
    }  
}  
  
template<class Real>  
void BM_SinPi2(benchmark::State& state)  
{  
  
    std::mt19937 gen(323723);  
    std::uniform_real_distribution<Real> dis(-0.95, 0.95);  
  
    for (auto _ : state)  
    {  
        Real arg = dis(gen);  
        benchmark::DoNotOptimize(sin_pi2(arg));  
    }  
}  
BENCHMARK_TEMPLATE(BM_SinPi2, float);  
BENCHMARK_TEMPLATE(BM_SinPi2, double);  
  
template<class Real>  
void BM_Sin(benchmark::State& state)  
{  
  
    std::mt19937 gen(323723);  
    std::uniform_real_distribution<Real> dis(-0.95, 0.95);  
  
    for (auto _ : state)  
    {  
        Real arg = dis(gen);  
        benchmark::DoNotOptimize(std::sin(arg));  
    }  
}  
BENCHMARK_TEMPLATE(BM_Sin, float);  
BENCHMARK_TEMPLATE(BM_Sin, double);  
  
BENCHMARK_MAIN();  
```  
  
The performance is shown below:  
  
```bash  
BM_SinPi<float>         88.7 ns         88.6 ns      7885039  
BM_SinPi<double>         141 ns          141 ns      4972259  
BM_SinPi2<float>        34.0 ns         34.0 ns     20603917  
BM_SinPi2<double>       48.9 ns         48.8 ns     14342610  
BM_Sin<float>           12.5 ns         12.5 ns     56321679  
BM_Sin<double>          35.1 ns         35.1 ns     19925642  
```  
  
Is it reasonable to replace the implementation or is there something fundamental I'm missing?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-05-01 18:32:53 UTC  
> Url: https://github.com/boostorg/math/issues/200#issuecomment-488370337  

Your version returns the wrong sign when reducing by an even number, for example when the argument is 18.6282 you return -0.920005 but it should be +0.920005.  
The way this is handled in the current code is to do an odd/even check on the integer reduction - this currently uses itrunc to convert the float to an integer, and it turns out that all the extra time is disappearing inside there (not sure why, but these are such fast functions, it might just be a function call overhead?  Changing itrunc to a straight cast to int puts the performance on a par with yours, at the cost of removing some error checking and no longer with multiprecision/class types.  
I could specialize for float/double etc, but I'd like to see why itrunc is so slow first...

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-05-01 18:57:45 UTC  
> Updated at: 2019-05-01 18:59:04 UTC  
> Url: https://github.com/boostorg/math/issues/200#issuecomment-488379645  

My static_cast is compiling down to a `cvttsd2si` + a bunch of insane stack writes which serve no obvious purpose. But I guess it's not possible to keep gcc from doing insane stack writes so that can be treated as a constant. Presumably it's hard for `itrunc` to do better than this . . .   
  
In any case, I fixed the bug you found:  
  
```cpp  
#include "math_unit_test.hpp"  
#include <boost/math/special_functions/sin_pi.hpp>  
#include <boost/multiprecision/float128.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
using std::sin;  
using boost::math::constants::pi;  
using boost::multiprecision::float128;  
using boost::multiprecision::cpp_bin_float_100;  
  
template<class Real>  
Real sin_pi2(Real x)  
{  
    using std::sin;  
    using std::floor;  
    using boost::math::constants::pi;  
    if(x < 0) {  
      return -sin_pi2(-x);  
    }  
  
    Real integer_part = floor(x);  
    Real rem = x - integer_part;  
  
    if (rem > 0.5) {  
        rem = 1 - rem;  
    }  
  
    long i = static_cast<long>(integer_part);  
  
    if (i & 1) {  
        return -sin(pi<Real>()*rem);  
    } else {  
        return sin(pi<Real>()*rem);  
    }  
}  
  
template<class Real>  
void test_ulp()  
{  
    Real x = 0;  
    Real max_x = 10;  
    Real step = std::numeric_limits<Real>::epsilon();  
    while (x < max_x) {  
        Real expected = static_cast<Real>(sin(pi<cpp_bin_float_100>()*cpp_bin_float_100(x)));  
        Real computed = boost::math::sin_pi<Real>(x);  
        if (!CHECK_ULP_CLOSE(expected, computed, 2)) {  
            std::cerr << "  Boost Baddie at x = " << x << "\n";  
        }  
  
        computed = sin_pi2<Real>(x);  
        if (!CHECK_ULP_CLOSE(expected, computed, 2)) {  
            std::cerr << "  My Baddie at x = " << x << "\n";  
        }  
        x += step;  
    }  
}  
  
int main()  
{  
    test_ulp<float>();  
}  
```  
  
Yours is still more accurate for reasons I can't understand right now. (Change the ULP tolerance to 1 to see it.)

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-05-02 18:45:51 UTC  
> Url: https://github.com/boostorg/math/issues/200#issuecomment-488786854  

I've managed to get the existing code up to the same speed as yours (at least as far as msvc is concerned), but I want to test a bit more and do at least cos_pi as well before pushing...

---

## Comment 4

> Username: jzmaddock  
> Created at: 2019-05-03 18:21:10 UTC  
> Url: https://github.com/boostorg/math/issues/200#issuecomment-489192639  

Can you try out current develop?  
  
Note that on some platforms you may need to disable double->long double promotion inside sin_pi/cos_pi to achieve max performance (see below).  
  
Here's the updated benchmark:  
  
```  
// test.cpp : This file contains the 'main' function. Program execution begins  
// and ends there.  
//  
  
#include <benchmark/benchmark.h>  
#include <algorithm>  
#include <boost/math/special_functions/sin_pi.hpp>  
#include <boost/math/special_functions/cos_pi.hpp>  
#include <iostream>  
#include <random>  
  
template <class Real>  
void BM_Sin(benchmark::State& state) {  
  std::mt19937 gen(323723);  
  std::uniform_real_distribution<Real> dis(-50.95, 50.95);  
  
  for (auto _ : state) {  
    Real arg = dis(gen);  
    benchmark::DoNotOptimize(std::sin(arg));  
  }  
}  
BENCHMARK_TEMPLATE(BM_Sin, float);  
BENCHMARK_TEMPLATE(BM_Sin, double);  
  
template <class Real>  
void BM_SinPi(benchmark::State& state) {  
  std::mt19937 gen(323723);  
  std::uniform_real_distribution<Real> dis(-50.95, 50.95);  
  
  for (auto _ : state) {  
    Real arg = dis(gen);  
    benchmark::DoNotOptimize(boost::math::sin_pi(arg));  
  }  
}  
template <class Real>  
void BM_SinPi_no_promote(benchmark::State& state) {  
  std::mt19937 gen(323723);  
  std::uniform_real_distribution<Real> dis(-50.95, 50.95);  
  
  typedef boost::math::policies::policy<  
      boost::math::policies::promote_float<false>,  
      boost::math::policies::promote_double<false> >  
      no_promote_policy;  
  
  for (auto _ : state) {  
    Real arg = dis(gen);  
    benchmark::DoNotOptimize(boost::math::sin_pi(arg, no_promote_policy()));  
  }  
}  
BENCHMARK_TEMPLATE(BM_SinPi, float);  
BENCHMARK_TEMPLATE(BM_SinPi, double);  
BENCHMARK_TEMPLATE(BM_SinPi_no_promote, float);  
BENCHMARK_TEMPLATE(BM_SinPi_no_promote, double);  
  
template <class Real>  
Real sin_pi2(Real x) {  
  using boost::math::constants::pi;  
  using std::floor;  
  using std::sin;  
  if (x < 0) {  
    return -sin_pi2(-x);  
  }  
  
  Real integer_part = floor(x);  
  Real rem = x - integer_part;  
  if (rem > 0.5) {  
    rem = 1 - rem;  
    integer_part += 1;  
  }  
  
  long i = static_cast<long>(integer_part);  
  
  if (i & 1) {  
    return -sin(pi<Real>() * rem);  
  } else {  
    return sin(pi<Real>() * rem);  
  }  
}  
  
template <class Real>  
void BM_SinPi2(benchmark::State& state) {  
  std::mt19937 gen(323723);  
  std::uniform_real_distribution<Real> dis(-50.95, 50.95);  
  
  for (auto _ : state) {  
    Real arg = dis(gen);  
    benchmark::DoNotOptimize(sin_pi2(arg));  
  }  
}  
BENCHMARK_TEMPLATE(BM_SinPi2, float);  
BENCHMARK_TEMPLATE(BM_SinPi2, double);  
  
template <class Real>  
void BM_Cos(benchmark::State& state) {  
  std::mt19937 gen(323723);  
  std::uniform_real_distribution<Real> dis(-50.95, 50.95);  
  
  for (auto _ : state) {  
    Real arg = dis(gen);  
    benchmark::DoNotOptimize(std::cos(arg));  
  }  
}  
BENCHMARK_TEMPLATE(BM_Cos, float);  
BENCHMARK_TEMPLATE(BM_Cos, double);  
  
template <class Real>  
void BM_CosPi(benchmark::State& state) {  
  std::mt19937 gen(323723);  
  std::uniform_real_distribution<Real> dis(-50.95, 50.95);  
  
  for (auto _ : state) {  
    Real arg = dis(gen);  
    benchmark::DoNotOptimize(boost::math::cos_pi(arg));  
  }  
}  
template <class Real>  
void BM_CosPi_no_promote(benchmark::State& state) {  
  std::mt19937 gen(323723);  
  std::uniform_real_distribution<Real> dis(-50.95, 50.95);  
  
  typedef boost::math::policies::policy<  
      boost::math::policies::promote_float<false>,  
      boost::math::policies::promote_double<false> >  
      no_promote_policy;  
  
  for (auto _ : state) {  
    Real arg = dis(gen);  
    benchmark::DoNotOptimize(boost::math::cos_pi(arg, no_promote_policy()));  
  }  
}  
BENCHMARK_TEMPLATE(BM_CosPi, float);  
BENCHMARK_TEMPLATE(BM_CosPi, double);  
BENCHMARK_TEMPLATE(BM_CosPi_no_promote, float);  
BENCHMARK_TEMPLATE(BM_CosPi_no_promote, double);  
  
  
BENCHMARK_MAIN();  
```  
  
Output from msvc:  
  
```  
Run on (8 X 1992 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x4)  
  L1 Instruction 32K (x4)  
  L2 Unified 262K (x4)  
  L3 Unified 8388K (x1)  
----------------------------------------------------------------------  
Benchmark                            Time             CPU   Iterations  
----------------------------------------------------------------------  
BM_Sin<float>                     41.3 ns         41.7 ns     17230769  
BM_Sin<double>                    28.6 ns         28.9 ns     24888889  
BM_SinPi<float>                   34.9 ns         35.3 ns     20363636  
BM_SinPi<double>                  27.0 ns         27.3 ns     26352941  
BM_SinPi_no_promote<float>        34.9 ns         34.5 ns     19478261  
BM_SinPi_no_promote<double>       26.9 ns         26.7 ns     26352941  
BM_SinPi2<float>                  35.0 ns         35.3 ns     20363636  
BM_SinPi2<double>                 27.5 ns         27.6 ns     24888889  
BM_Cos<float>                     35.8 ns         36.1 ns     19478261  
BM_Cos<double>                    29.1 ns         29.1 ns     26352941  
BM_CosPi<float>                   36.4 ns         36.9 ns     19478261  
BM_CosPi<double>                  28.1 ns         28.5 ns     26352941  
BM_CosPi_no_promote<float>        36.6 ns         36.9 ns     19478261  
BM_CosPi_no_promote<double>       31.0 ns         29.9 ns     20363636  
```

---

## Comment 5

> Username: NAThompson  
> Created at: 2019-05-03 20:29:05 UTC  
> Url: https://github.com/boostorg/math/issues/200#issuecomment-489229792  

I find it very strange that sine is slower in float precision than double on MSVC; when I run your benchmark using g++-8 I don't see that. In any case, here's the output I get with  
  
```bash  
g++-8 -I./include -I../../../boost --std=c++17 -march=native -O3 -ffast-math test.cpp -lbenchmark -lbenchmark_main -pthread  
```  
  
```bash  
2019-05-03 16:22:29  
Running ./a.out  
Run on (2 X 3300 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x2)  
  L1 Instruction 32K (x2)  
  L2 Unified 256K (x2)  
  L3 Unified 3072K (x1)  
Load Average: 0.17, 0.31, 0.19  
----------------------------------------------------------------------  
Benchmark                            Time             CPU   Iterations  
----------------------------------------------------------------------  
BM_Sin<float>                     20.6 ns         20.6 ns     33969206  
BM_Sin<double>                    44.7 ns         44.6 ns     15679269  
BM_SinPi<float>                   45.6 ns         45.2 ns     15457933  
BM_SinPi<double>                  80.6 ns         80.5 ns      8685480  
BM_SinPi_no_promote<float>        36.8 ns         36.5 ns     19181025  
BM_SinPi_no_promote<double>       53.0 ns         52.9 ns     13229723  
BM_SinPi2<float>                  37.8 ns         37.7 ns     18576036  
BM_SinPi2<double>                 53.7 ns         53.3 ns     13103178  
BM_Cos<float>                     20.4 ns         20.4 ns     34338087  
BM_Cos<double>                    44.7 ns         44.7 ns     15669084  
BM_CosPi<float>                   45.5 ns         45.5 ns     15386990  
BM_CosPi<double>                  85.7 ns         85.7 ns      8165762  
BM_CosPi_no_promote<float>        33.7 ns         33.7 ns     20775641  
BM_CosPi_no_promote<double>       52.7 ns         52.7 ns     13283941  
```  
  
So sin_pi_no_promote is a very much acceptable cost above calls to sine, but I'm confused as to why gcc is doing this long double promotion in the first place.  
  
Of course, nothing we can do about it, but clang 6.0 does a very poor job with this:  
  
```bash  
BM_Sin<float>                     92.1 ns         92.0 ns      7596889  
BM_Sin<double>                     116 ns          116 ns      6039997  
BM_SinPi<float>                    110 ns          109 ns      6393240  
BM_SinPi<double>                   286 ns          286 ns      2448960  
BM_SinPi_no_promote<float>         101 ns          101 ns      6940553  
BM_SinPi_no_promote<double>        117 ns          117 ns      6006419  
BM_SinPi2<float>                   103 ns          102 ns      6830852  
BM_SinPi2<double>                  119 ns          119 ns      5905943  
BM_Cos<float>                     92.2 ns         92.1 ns      7602179  
BM_Cos<double>                     118 ns          118 ns      5918561  
BM_CosPi<float>                    109 ns          109 ns      6440883  
BM_CosPi<double>                   214 ns          214 ns      3274262  
BM_CosPi_no_promote<float>        94.6 ns         94.4 ns      7410933  
BM_CosPi_no_promote<double>        114 ns          114 ns      6150028  
```

---

## Comment 6

> Username: jzmaddock  
> Created at: 2019-05-04 07:37:17 UTC  
> Url: https://github.com/boostorg/math/issues/200#issuecomment-489303761  

>So sin_pi_no_promote is a very much acceptable cost above calls to sine, but I'm confused as to why gcc is doing this long double promotion in the first place.  
  
Ugh, history.  And something we may need to revisit, albeit a big breaking change.  
  
When the library was written, most code was 32-bit, most floating point code was x87, and there was next no difference in performance between double and long double functions.  Plus the C std libraries were typically implemented as long double functions which the float/double versions called internally.  So... it made a lot of sense to have "one true version" of each special function, and that was the long double version.  Oh and it did wonders for accuracy.  So we have code like this at the start of each function:  
  
```  
   typedef typename tools::promote_args<T1, T2, T3>::type result_type;  
   typedef typename policies::evaluation<result_type, Policy>::type value_type;  
```  
  
The result_type converts all the argument types to the canonical floating point type (including integer->double promotion), while the `evaluation_type` is the type used internally for the computation.  It is of course all controllable via the policies, but the default behaviour is to apply a single promotion (float->double or double->long double) so that the result is last bit accurate.  The issue is that double has become approx twice as fast as long double over the last few years.  We can change the default very easily, but it' a big breaking change.  
  
>Of course, nothing we can do about it, but clang 6.0 does a very poor job with this:  
  
Weird, is it using intrinsics or calling the C lib?  I suspect the latter?

---

## Comment 7

> Username: NAThompson  
> Created at: 2019-05-04 16:29:33 UTC  
> Url: https://github.com/boostorg/math/issues/200#issuecomment-489342015  

> but the default behaviour is to apply a single promotion (float->double or double->long double) so that the result is last bit accurate.  
  
I think 1ULP accurate is generally acceptable and half ULP accurate is more than expected; I'll try to take a look and see if your implementation satisfies this bound.  
  
In any case, Iooked into what clang was doing:  
  
![clang_sin_pi](https://user-images.githubusercontent.com/5459618/57181822-9eb6a400-6e66-11e9-91ba-13e4badc8d87.png)  
  
It's doing a lot of comparisons to a stack pointer which is taking a lot of time, especially since it appears that (say) [rip + 0x400f5] is just the value 0.5 and can be put in the instruction instead of the stack.  
  
At a high level, I see calls to __ieee_754_logl, which I did not expect:  
  
![clang_high_level](https://user-images.githubusercontent.com/5459618/57181901-6368a500-6e67-11e9-9c12-36eb64cb5721.png)  
  
On g++-8, the logl is not called:  
  
![gcc_high_level](https://user-images.githubusercontent.com/5459618/57181952-e1c54700-6e67-11e9-81e5-22b47a07ac2b.png)

---

## Comment 8

> Username: NAThompson  
> Created at: 2019-05-04 17:00:09 UTC  
> Url: https://github.com/boostorg/math/issues/200#issuecomment-489344857  

As to the ULP accuracy with `no_promote_policy`, I've been running this code for a few hours:  
  
```cpp  
#include "math_unit_test.hpp"  
#include <boost/math/special_functions/sin_pi.hpp>  
#include <boost/multiprecision/float128.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
using std::sin;  
using boost::math::constants::pi;  
using boost::multiprecision::float128;  
using boost::multiprecision::cpp_bin_float_100;  
  
  
template<class Real>  
void test_ulp()  
{  
    Real x = 10;  
    Real max_x = 1000;  
    typedef boost::math::policies::policy<  
      boost::math::policies::promote_float<false>,  
      boost::math::policies::promote_double<false> >  
      no_promote_policy;  
    Real step = std::numeric_limits<Real>::epsilon();  
    while (x < max_x) {  
        Real expected = static_cast<Real>(sin(pi<cpp_bin_float_100>()*cpp_bin_float_100(x)));  
        Real computed = boost::math::sin_pi<Real>(x, no_promote_policy());  
        if (!CHECK_ULP_CLOSE(expected, computed, 2)) {  
            std::cerr << "  Baddie at x = " << x << "\n";  
        }  
        x = std::nextafter(x, std::numeric_limits<float>::max());  
    }  
}  
  
int main()  
{  
    test_ulp<float>();  
    test_ulp<double>();  
    return boost::math::test::report_errors();  
}  
```  
  
I haven't found any that are more than 2 representables incorrect, and lots that are 2 representables incorrect. Is that an acceptable backwards incompatible change? Kinda a grey area; I'd definitely say go for it if it was only 1representable incorrect. . .

---

## Comment 9

> Username: jzmaddock  
> Created at: 2019-05-04 17:43:59 UTC  
> Url: https://github.com/boostorg/math/issues/200#issuecomment-489348732  

>At a high level, I see calls to __ieee_754_logl, which I did not expect:  
  
Huh??  
  
I assume this is nothing to do with sin_pi and more to do with the random number generator or something?  I'm sure those *two* log calls are the reason for the slowdown?  
  
>I haven't found any that are more than 2 representables incorrect, and lots that are 2 representables incorrect. Is that an acceptable backwards incompatible change? Kinda a grey area; I'd definitely say go for it if it was only 1representable incorrect. . .  
  
It's probably fine for this small function, I would prefer to have a consistent policy throughout though rather than an ad-hoc one with different functions doing different things.

---

## Comment 10

> Username: NAThompson  
> Created at: 2019-05-04 17:56:49 UTC  
> Url: https://github.com/boostorg/math/issues/200#issuecomment-489349825  

> I assume this is nothing to do with sin_pi and more to do with the random number generator or something? I'm sure those two log calls are the reason for the slowdown?  
  
Yup, sry my bad. Clang is doing something strange with the RNG.

---

## Comment 11

> Username: shaneasd  
> Created at: 2019-05-08 16:18:52 UTC  
> Url: https://github.com/boostorg/math/issues/200#issuecomment-490552762  

Overall I think it's probably a bad idea but you can replace  
```  
    long i = static_cast<long>(integer_part);  
  
    if (i & 1) {  
        return -sin(pi<Real>() * rem);  
    }  
    else {  
        return sin(pi<Real>() * rem);  
    }  
```  
with something like  
```  
    int64_t i = static_cast<int64_t>(integer_part);  
    double result = sin(pi<double>() * rem);  
    int64_t sign = (i & 1) << 63;  
    int64_t temp = *reinterpret_cast<int64_t*>(&result) | sign;  
    return *reinterpret_cast<double*>(&temp);  
```  
for about a 1% speedup in my (admittedly imprecise) benchmarks. Naturally the int64_t and 63 are specific to double so would have to be compile time calculated for other types which may not even be possible for some. You could of course hide this behind an inlined function to make it less ugly and provide the optimization only where the type supports it. Depends how determined you are to squeeze out performance vs maintainability (and also how representative my benchmarks are). I'm also not sure how consistent the bit layout of floats are which could put a big hole in the whole idea.

---

## Comment 12

> Username: jzmaddock  
> Created at: 2019-05-08 17:06:36 UTC  
> Url: https://github.com/boostorg/math/issues/200#issuecomment-490569610  

I think I'd rather not start relying on reinterpret_casts and undefined behaviour, unless the difference is huge.

---

## Comment 13

> Username: jzmaddock  
> Created at: 2019-05-13 17:37:39 UTC  
> Url: https://github.com/boostorg/math/issues/200#issuecomment-491917036  

Assuming this is fixed and closing down.

---

## Comment 14

> Username: NAThompson  
> Created at: 2019-05-13 17:43:50 UTC  
> Url: https://github.com/boostorg/math/issues/200#issuecomment-491919160  

Agreed; I think it's fixed.
