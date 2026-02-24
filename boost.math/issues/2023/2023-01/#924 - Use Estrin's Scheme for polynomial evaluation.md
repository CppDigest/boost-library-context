# #924 - Use Estrin's Scheme for polynomial evaluation [Closed]

> Username: thomasahle  
> Created at: 2023-01-23 18:00:00 UTC  
> Updated at: 2023-02-04 18:53:47 UTC  
> Closed at: 2023-02-04 18:53:47 UTC  
> Url: https://github.com/boostorg/math/issues/924  

According to https://www.boost.org/doc/libs/1_81_0/libs/math/doc/html/math_toolkit/rational.html Boost currently evaluates Polynomials (and rational functions) using Horner's rule.  
However, a simple change of the algorithm, known as [Estrin's scheme](https://en.wikipedia.org/wiki/Estrin%27s_scheme) is known to both improve performance and reduce numerical instability.  
  
Maybe there's a reason this method is not currently used?  
Otherwise I can provide a pull request.  
  
There are also quite a few other algorithms for polynomial evaluation: https://en.wikipedia.org/wiki/Polynomial_evaluation though most of them requires some preprocessing of the polynomial, so they are only useful if you are going to evaluate it many times. I don't know if something like that would be useful for Boost?

---

## Comment 1

> Username: mborland  
> Created at: 2023-01-23 18:40:11 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1400807315  

There is no compelling reason we use Horner's rule over any other algorithm to my knowledge. If you provide a pull request I will certainly review it. For performance comparison we typically use google benchmark. You'll find numerous examples in reporting/performance folder.

---

## Comment 2

> Username: NAThompson  
> Created at: 2023-01-23 19:15:13 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1400849173  

@thomasahle : This is a good idea, and would be a welcome addition by me!  
  
Only thing I worry about is that it's sometimes very hard to get the AVX instructions required by the scheme to generate; probably gotta spend some time with godbolt.  
  
In any case, maybe I should try Estrin's scheme to help with the struggles I'm having in my other [MR](https://github.com/boostorg/math/pull/921#issuecomment-1399344667)!

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2023-01-25 03:19:07 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1403044607  

I remember reading somewhere that depending on the ratio of some two values, maybe the variable and something else, Horner's method was more accurate when the ratio was above x but another method, perhaps the naive one, was more accurate below x. Now I'm going to have to go back through the books to find it!

---

## Comment 4

> Username: thomasahle  
> Created at: 2023-01-25 06:35:07 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1403160077  

@NAThompson I can try coding the AVX manually, but from my experience Estrin can often give a factor 2 speedup over Horner, even without AVX. I honestly don't get how it happens simply from rearranging the operations but it works for me at least 😅. Hopefully the Google benchmarks will agree.  
For numerical precision I'm not sure how best to test that.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2023-01-25 11:41:17 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1403479413  

Generally speaking, for simple code like this, trying to second guess the optimizer results in worse code in most cases.  
  
We also have a few different methods for polynomial evaluation already, see https://www.boost.org/doc/libs/1_72_0/libs/math/doc/html/math_toolkit/tuning.html  The second order Horner which is the default, is already 1.5-2x faster than naive Horner.  BTW Most of the code is machine generated via: https://github.com/boostorg/math/blob/develop/tools/generate_rational_code.cpp  
  
That said, this is all old code now, so may well be worth revisiting and testing for modern processors.  
  
There's also some performance testing in https://github.com/boostorg/math/blob/develop/reporting/performance/test_poly_method.cpp

---

## Comment 6

> Username: NAThompson  
> Created at: 2023-01-25 15:49:45 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1403829362  

> For numerical precision I'm not sure how best to test that.  
  
@thomasahle : We generally use my garbage code [ulps_plot.hpp](https://www.boost.org/doc/libs/1_81_0/boost/math/tools/ulps_plot.hpp). All the plots I made [here](https://github.com/boostorg/math/pull/921#issuecomment-1403046677) are made with that tool. For an introduction to ulps plots, see [here](https://blogs.mathworks.com/cleve/2017/01/23/ulps-plots-reveal-math-function-accurary/).

---

## Comment 7

> Username: thomasahle  
> Created at: 2023-01-25 19:03:29 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1404097363  

@jzmaddock There's a cpp program for generating the cpp programs? I guess you don't fully trust the compiler after all :D  
  
Where do I see the generated code measured for the tables?

---

## Comment 8

> Username: NAThompson  
> Created at: 2023-01-25 21:08:41 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1404225963  

> Where do I see the generated code measured for the tables?  
  
For instance:  
  
include/boost/math/tools/detail/polynomial_horner3_9.hpp

---

## Comment 9

> Username: NAThompson  
> Created at: 2023-01-25 21:21:50 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1404239695  

@thomasahle : Just a quick google/benchmark I hacked up for comparison:  
  
  
  
```cpp  
//  (C) Copyright Nick Thompson 2023.  
//  Use, modification and distribution are subject to the  
//  Boost Software License, Version 1.0. (See accompanying file  
//  LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
#include <complex>  
#include <random>  
#include <array>  
#include <vector>  
#include <iostream>  
#include <benchmark/benchmark.h>  
  
template<typename Real, typename RealOrComplex>  
inline auto horners_method(const Real* coeffs, long n, RealOrComplex z) {  
    RealOrComplex result = coeffs[n - 1];  
    for (long i = n - 2; i >= 0; --i) {  
        if constexpr (std::is_same_v<Real, RealOrComplex>) {  
           result = std::fma(result, z, coeffs[i]);  
        } else {  
           result = result * z + coeffs[i];  
        }  
    }  
    return result;  
}  
  
  
template<class Real>  
void HornersMethodRealCoeffsRealArg(benchmark::State& state)  
{  
    long n = state.range(0);  
    std::random_device rd;  
    auto seed = rd();  
    std::mt19937_64 mt(seed);  
    std::uniform_real_distribution<Real> unif(-10, 10);  
  
    std::vector<Real> c(n);  
    std::fill(c.begin(), c.end(), unif(mt));  
    Real x = unif(mt);  
    for (auto _ : state)  
    {  
        Real output = horners_method(c.data(), n, x);  
        benchmark::DoNotOptimize(output);  
        x += std::sqrt(std::numeric_limits<Real>::epsilon());  
    }  
    state.SetComplexityN(state.range(0));      
}  
  
BENCHMARK_TEMPLATE(HornersMethodRealCoeffsRealArg, float)->Range(1, 1<<15)->Complexity();  
BENCHMARK_TEMPLATE(HornersMethodRealCoeffsRealArg, double)->Range(1, 1 << 15)->Complexity();  
  
template<class Real>  
void HornersMethodRealCoeffsComplexArg(benchmark::State& state)  
{  
    long n = state.range(0);  
    std::random_device rd;  
    auto seed = rd();  
    std::mt19937_64 mt(seed);  
    std::uniform_real_distribution<Real> unif(-10, 10);  
  
    std::vector<Real> c(n);  
    std::fill(c.begin(), c.end(), unif(mt));  
    std::complex<Real> x{unif(mt), unif(mt)};  
    for (auto _ : state)  
    {  
        std::complex<Real> output = horners_method(c.data(), n, x);  
        benchmark::DoNotOptimize(output);  
        x += std::sqrt(std::numeric_limits<Real>::epsilon());  
    }  
    state.SetComplexityN(state.range(0));      
}  
  
BENCHMARK_TEMPLATE(HornersMethodRealCoeffsComplexArg, float)->Range(1, 1<<15)->Complexity();  
BENCHMARK_TEMPLATE(HornersMethodRealCoeffsComplexArg, double)->Range(1, 1 << 15)->Complexity();  
  
  
template<typename Real, typename RealOrComplex>  
inline auto second_order_horner(const Real* coeffs, long n, RealOrComplex z) {  
   RealOrComplex p1 = coeffs[n - 1];  
   RealOrComplex p2 = coeffs[n - 2];  
   RealOrComplex zsq = z*z;  
   for (long i = n - 3; i >= 0; i -= 2) {  
       p1 = p1 * zsq + coeffs[i];  
       p2 = p2 * zsq + coeffs[i-1];  
   }  
   return p1 + z*p2;  
}  
  
  
template<class Real>  
void SecondOrderHornersMethodRealCoeffsRealArg(benchmark::State& state)  
{  
    long n = state.range(0);  
    std::random_device rd;  
    auto seed = rd();  
    std::mt19937_64 mt(seed);  
    std::uniform_real_distribution<Real> unif(-10, 10);  
  
    std::vector<Real> c(n);  
    std::fill(c.begin(), c.end(), unif(mt));  
    Real x = unif(mt);  
    for (auto _ : state)  
    {  
        Real output = second_order_horner(c.data(), n, x);  
        benchmark::DoNotOptimize(output);  
        x += std::sqrt(std::numeric_limits<Real>::epsilon());  
    }  
    state.SetComplexityN(state.range(0));      
}  
  
BENCHMARK_TEMPLATE(SecondOrderHornersMethodRealCoeffsRealArg, float)->Range(1, 1<<15)->Complexity();  
BENCHMARK_TEMPLATE(SecondOrderHornersMethodRealCoeffsRealArg, double)->Range(1, 1 << 15)->Complexity();  
  
  
BENCHMARK_MAIN();  
  
```  
  
If you'd like to add your Estrin to this it'd be pretty useful I think . . .

---

## Comment 10

> Username: thomasahle  
> Created at: 2023-01-26 00:45:00 UTC  
> Updated at: 2023-01-26 00:58:52 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1404416665  

Ok, I ran the benchmarks now. I got pretty good results, unless I did something wrong, which is very possible. Looks like a factor 2-3 improvement over Horner and Second Order Horner for larger n.  
  
```  
Run on (16 X 2300 MHz CPU s)  
CPU Caches:  
  L1 Data 32 KiB  
  L1 Instruction 32 KiB  
  L2 Unified 256 KiB (x8)  
  L3 Unified 16384 KiB  
Load Average: 2.31, 2.39, 2.54  
--------------------------------------------------------------------------------------------------  
Benchmark                                                        Time             CPU   Iterations  
--------------------------------------------------------------------------------------------------  
HornersMethodRealCoeffsRealArg<float>/1                      0.481 ns        0.481 ns   1000000000  
HornersMethodRealCoeffsRealArg<float>/8                       3.10 ns         3.10 ns    223132461  
HornersMethodRealCoeffsRealArg<float>/64                      31.1 ns         31.1 ns     22366933  
HornersMethodRealCoeffsRealArg<float>/512                      448 ns          448 ns      1475651  
HornersMethodRealCoeffsRealArg<float>/4096                    3933 ns         3931 ns       174970  
HornersMethodRealCoeffsRealArg<float>/32768                  31729 ns        31726 ns        21835  
HornersMethodRealCoeffsRealArg<float>_BigO                    0.97 N          0.97 N  
HornersMethodRealCoeffsRealArg<float>_RMS                        0 %             0 %  
HornersMethodRealCoeffsRealArg<double>/1                     0.258 ns        0.257 ns   1000000000  
HornersMethodRealCoeffsRealArg<double>/8                      2.44 ns         2.44 ns    281804677  
HornersMethodRealCoeffsRealArg<double>/64                     30.4 ns         30.4 ns     23361289  
HornersMethodRealCoeffsRealArg<double>/512                     450 ns          450 ns      1571946  
HornersMethodRealCoeffsRealArg<double>/4096                   3919 ns         3919 ns       179403  
HornersMethodRealCoeffsRealArg<double>/32768                 32309 ns        32297 ns        20706  
HornersMethodRealCoeffsRealArg<double>_BigO                   0.99 N          0.99 N  
HornersMethodRealCoeffsRealArg<double>_RMS                       1 %             1 %  
SecondOrderHornersMethodRealCoeffsRealArg<float>/1            1.00 ns        1.000 ns    645607563  
SecondOrderHornersMethodRealCoeffsRealArg<float>/8            2.15 ns         2.15 ns    310807211  
SecondOrderHornersMethodRealCoeffsRealArg<float>/64           26.7 ns         26.7 ns     26769564  
SecondOrderHornersMethodRealCoeffsRealArg<float>/512           438 ns          438 ns      1636317  
SecondOrderHornersMethodRealCoeffsRealArg<float>/4096         3977 ns         3976 ns       175106  
SecondOrderHornersMethodRealCoeffsRealArg<float>/32768       30364 ns        30353 ns        23074  
SecondOrderHornersMethodRealCoeffsRealArg<float>_BigO         0.93 N          0.93 N  
SecondOrderHornersMethodRealCoeffsRealArg<float>_RMS             1 %             1 %  
SecondOrderHornersMethodRealCoeffsRealArg<double>/1           35.4 ns         35.2 ns     20775582  
SecondOrderHornersMethodRealCoeffsRealArg<double>/8           2.34 ns         2.30 ns    296232348  
SecondOrderHornersMethodRealCoeffsRealArg<double>/64          32.0 ns         30.9 ns     24793506  
SecondOrderHornersMethodRealCoeffsRealArg<double>/512          441 ns          441 ns      1388255  
SecondOrderHornersMethodRealCoeffsRealArg<double>/4096        3962 ns         3960 ns       180663  
SecondOrderHornersMethodRealCoeffsRealArg<double>/32768      31181 ns        31144 ns        22893  
SecondOrderHornersMethodRealCoeffsRealArg<double>_BigO        0.95 N          0.95 N  
SecondOrderHornersMethodRealCoeffsRealArg<double>_RMS            1 %             1 %  
EstrinsMethodRealCoeffsRealArg<float>/1                       2.82 ns         2.82 ns    241334372  
EstrinsMethodRealCoeffsRealArg<float>/8                       11.1 ns         11.1 ns     61738195  
EstrinsMethodRealCoeffsRealArg<float>/64                      26.1 ns         26.1 ns     27562311  
EstrinsMethodRealCoeffsRealArg<float>/512                      139 ns          139 ns      5021485  
EstrinsMethodRealCoeffsRealArg<float>/4096                    1068 ns         1067 ns       645542  
EstrinsMethodRealCoeffsRealArg<float>/32768                   8637 ns         8632 ns        72112  
EstrinsMethodRealCoeffsRealArg<float>_BigO                    0.26 N          0.26 N  
EstrinsMethodRealCoeffsRealArg<float>_RMS                        0 %             0 %  
EstrinsMethodRealCoeffsRealArg<double>/1                      2.87 ns         2.87 ns    229098074  
EstrinsMethodRealCoeffsRealArg<double>/8                      12.5 ns         12.5 ns     54883881  
EstrinsMethodRealCoeffsRealArg<double>/64                     27.2 ns         27.2 ns     26123010  
EstrinsMethodRealCoeffsRealArg<double>/512                     163 ns          162 ns      4516595  
EstrinsMethodRealCoeffsRealArg<double>/4096                   1386 ns         1382 ns       518822  
EstrinsMethodRealCoeffsRealArg<double>/32768                 14601 ns        14590 ns        46269  
EstrinsMethodRealCoeffsRealArg<double>_BigO                   0.03 NlgN       0.03 NlgN  
EstrinsMethodRealCoeffsRealArg<double>_RMS                       1 %             1 %  
```  
  
Where I used this version of Estrin:  
```  
template<typename Real, typename RealOrComplex>  
inline auto estrin(const Real* coeffs, long n, RealOrComplex z) {  
    if (n == 1)  
        return coeffs[0];  
  
    RealOrComplex ds[n/2];  
    for (int i = 0; i < n / 2; i++) {  
        ds[i] = coeffs[2*i] + coeffs[2*i+1] * z;  
    }  
    n /= 2;  
  
    while (n != 1) {  
        z = z * z;  
        for (int i = 0; i < n / 2; i++) {  
            ds[i] = ds[2*i] + ds[2*i+1] * z;  
        }  
        n /= 2;  
    }  
  
    return ds[0];  
}  
```  
  
Note this only supports `n` which are powers of two, which is what the test uses.  
Actually I don't really understand what the SecondOrder method does in the case n=1. Looks like it reads memory out of bounds?  
  
I normally have `n` as a template argument, to make sure the compiler will unroll all the loops, and not actually allocate an array. But this test seems to use `n` values much larger than I'm used to (up to 32K).

---

## Comment 11

> Username: NAThompson  
> Created at: 2023-01-26 00:57:08 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1404428526  

> Actually I don't really understand what the SecondOrder method does in the case n=1. Looks like it reads memory out of bounds?  
  
@thomasahle : Yeah, I just hacked this up really quick as an example; I should've actually made a bit of effort  . . .

---

## Comment 12

> Username: thomasahle  
> Created at: 2023-01-26 00:58:13 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1404429133  

Here is a version that doesn't assume n is a power of two. It runs just as fast.  
```  
template<typename Real, typename RealOrComplex>  
inline auto estrin2(const Real* coeffs, long n, RealOrComplex z) {  
    if (n == 1)  
        return coeffs[0];  
  
    RealOrComplex ds[(n+1)/2];  
    for (int i = 0; i < n / 2; i++) {  
        ds[i] = coeffs[2*i] + coeffs[2*i+1] * z;  
    }  
    if ((n & 1) == 1) ds[n / 2] = coeffs[n - 1];  
    n = (n + 1) / 2;  
  
    while (n != 1) {  
        z = z * z;  
        for (int i = 0; i < n / 2; i++) {  
            ds[i] = ds[2*i] + ds[2*i+1] * z;  
        }  
        if ((n & 1) == 1) ds[n / 2] = ds[n - 1];  
        n = (n + 1) / 2;  
    }  
  
    return ds[0];  
}  
```  
  
I ran the benchmarks on a "Intel(R) Core(TM) i9-9880H CPU @ 2.30GHz" with "-O3 and -match=native".

---

## Comment 13

> Username: NAThompson  
> Created at: 2023-01-26 00:59:06 UTC  
> Updated at: 2023-01-26 01:00:07 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1404429805  

> I normally have n as a template argument, to make sure the compiler will unroll all the loops, and not actually allocate an array.  
  
At least in my [wavelet MR](https://github.com/boostorg/math/pull/921/files), I do know the polynomial coefficients at compile time and ostensibly that's where this method would really shine. It just would be a bit awkward in the googlebenchmark file but who cares.

---

## Comment 14

> Username: thomasahle  
> Created at: 2023-01-26 01:03:39 UTC  
> Updated at: 2023-01-26 01:05:40 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1404433127  

If I run this on godbolt with `n` known in advance, it gives me the same code as a manual Estrin like  
  
```  
  double mult5(double x) {  
    // P4(x) = (C0 + C1x) + (C2 + C3x) x2 + C4x4  
    // We basically do bottom-up horner  
    double c0 = ms[0] + ms[1] * x;  // C0 = M0 + M1 x  
    double c1 = ms[2] + ms[3] * x;  // C1 = M2 + M3 x  
    double c2 = ms[4];              // C2 = M4  
    // Combine first two terms using horner with x^2  
    x = x * x;  
    double d0 = c0 + c1 * x;        // D0 = C0 + C1 x^2  
    double d1 = c2;                 // D1 = C2  
    // Combine last term using horner with x^4  
    x = x * x;  
    double h = d0 + d1 * x;         // H = D0 + D1 x^4  
    return h;  
  }  
```  
  
I wonder if the lack of compile time unfolding is why the n=8 case above suffers...

---

## Comment 15

> Username: thomasahle  
> Created at: 2023-01-26 01:34:33 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1404454348  

There are also some methods that does [preprocessing on the coefficients](https://en.wikipedia.org/wiki/Polynomial_evaluation#Evaluation_with_preprocessing), and are able to use just n/2 multiplications. In my experiments these are even faster than Estrin. But of course, it requires a different API.

---

## Comment 16

> Username: NAThompson  
> Created at: 2023-01-26 04:01:31 UTC  
> Updated at: 2023-01-26 04:06:21 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1404539571  

@thomasahle : I've applied your Estrin method to my [wavelet MR](https://github.com/boostorg/math/pull/921). The runtime goes from:  
  
```  
FourierTransformDaubechiesScaling<float>         421 ns  
FourierTransformDaubechiesScaling<double>        877 ns  
```  
  
to:  
  
```  
Run on (10 X 24.0614 MHz CPU s)  
CPU Caches:  
  L1 Data 64 KiB (x10)  
  L1 Instruction 128 KiB (x10)  
  L2 Unified 4096 KiB (x5)  
Load Average: 1.50, 1.48, 1.82  
------------------------------------------------------------------------------------  
Benchmark                                          Time             CPU   Iterations  
------------------------------------------------------------------------------------  
FourierTransformDaubechiesScaling<float>         378 ns          378 ns      1845601  
FourierTransformDaubechiesScaling<double>        874 ns          874 ns       797730  
```  
  
Mind if I add your code to that MR and add you to the copyright?  
  
> here are also some methods that does [preprocessing on the coefficients](https://en.wikipedia.org/wiki/Polynomial_evaluation#Evaluation_with_preprocessing),  
  
I've considered that. . . in the wavelet MR I know the polynomials ahead of time and could precondition them. However I need to run it under `perf` to see if this is actually the bottleneck. What I suspect is repeated evaluation of `std::exp` for complex argument is more of the problem . . . I also didn't see anything stated about the stability of the preconditioned polynomial.

---

## Comment 17

> Username: NAThompson  
> Created at: 2023-01-26 05:00:29 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1404565930  

Actually the 10 vanishing moment evaluation is way more dramatic:  
  
Before:  
  
```  
------------------------------------------------------------------------------------  
Benchmark                                          Time             CPU   Iterations  
------------------------------------------------------------------------------------  
FourierTransformDaubechiesScaling<float>         874 ns          874 ns       693179  
FourierTransformDaubechiesScaling<double>       1962 ns         1962 ns       355135  
```  
  
After:  
  
```  
------------------------------------------------------------------------------------  
Benchmark                                          Time             CPU   Iterations  
------------------------------------------------------------------------------------  
FourierTransformDaubechiesScaling<float>         555 ns          555 ns       973047  
FourierTransformDaubechiesScaling<double>       1306 ns         1306 ns       534319  
```

---

## Comment 18

> Username: thomasahle  
> Created at: 2023-01-26 05:17:24 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1404574840  

> Mind if I add your code to that MR and add you to the copyright?  
  
Definitely feel free to! Glad it works!  
  
I still wonder why in the Google benchmarks it was slower for small n < 10...  
  
> What I suspect is repeated evaluation of std::exp for complex argument is more of the problem . . .  
  
Isn't exp also evaluated by a polynomial?   
  
> I also didn't see anything stated about the stability of the preconditioned polynomial.   
  
I can find some papers on it. We can also try to test it with your ulps_plot code.

---

## Comment 19

> Username: NAThompson  
> Created at: 2023-01-26 05:50:00 UTC  
> Updated at: 2023-01-26 05:51:47 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1404587746  

> I still wonder why in the Google benchmarks it was slower for small n < 10...  
  
It wasn't slower; it was actually faster, just not as noticeable.  
  
> Isn't exp also evaluated by a polynomial?  
  
Well, the issue is relative expense. If I do the 3 vanishing moment wavelet, that corresponds to first computing z = exp(iω), and then evaluating a degree-3 polynomial in z. . . .  
  
> We can also try to test it with your ulps_plot code.  
  
Good idea. Is there a well-known algorithm/implementation, say in Mathematica? I guess we don't have to even code it up if we can just copy/paste coeffs.  
  
(Just added your code and added you to the copyright of [this MR](https://github.com/boostorg/math/pull/921).)

---

## Comment 20

> Username: thomasahle  
> Created at: 2023-01-26 06:23:55 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1404605363  

> Good idea. Is there a well-known algorithm/implementation, say in Mathematica? I guess we don't have to even code it up if we can just copy/paste coeffs.  
  
You mean specifically a "precomputed coefficients" computation for exp? I haven't seen it actually.   
Does Mathematica usually have publicly available code?   
  
I looked in https://core.ac.uk/download/pdf/52321127.pdf Chapter 5, but I don't see any code specifically for exp.  
Maybe in Knuth...   
  
I don't mind doing the precomputation though.   
  
> (Just added your code and added you to the copyright of [this MR](https://github.com/boostorg/math/pull/921).)  
  
Nice!

---

## Comment 21

> Username: mborland  
> Created at: 2023-01-26 15:41:26 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1405201083  

[This repo](https://github.com/samhocevar/lolremez) can provide pre-computed coefficients of the remez polynomial for a number of functions. exp is actually his [example](https://github.com/samhocevar/lolremez/wiki/Tutorial-1-of-5%3A-exp%28x%29-the-quick-way).

---

## Comment 22

> Username: NAThompson  
> Created at: 2023-01-26 16:04:24 UTC  
> Updated at: 2023-01-26 16:23:10 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1405234542  

> You mean specifically a "precomputed coefficients" computation for exp?   
  
Nah, I want to find the minimal operation polynomial for each of the coefficient sets [here](https://github.com/boostorg/math/blob/cbcba28d7e76552eceb3a34f7886e45258a7cedc/include/boost/math/special_functions/fourier_transform_daubechies_scaling.hpp#L29). I hope `std::exp` is already as optimized as we could realistically hope it could be.

---

## Comment 23

> Username: thomasahle  
> Created at: 2023-01-27 19:01:33 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1406951537  

I wrote python code for Knuth Eve:  
```  
import numpy as np  
from numpy.polynomial.polynomial import Polynomial  
from math import factorial as fac  
  
  
# Based on Knuth: "Evaluation of Polynomials By Computer"  
# https://dl.acm.org/doi/pdf/10.1145/355580.369074  
# and Eve: "The Evaluation of Polynomials"  
# and Overill and Wilson: "Data parallel evaluation of univariate  
# polynomials by the Knuth-Eve algorithm"  
# and Guillaume Revy "Implementation of binary floating-point  
# arithmetic on embedded integer processors - Polynomial evaluation-  
# based algorithms and certified code generation"  
  
  
class NoRealRoots(Exception):  
    pass  
  
  
def ke(p):  
    if p.degree() == 0:  
        return [(p.coef[0], 0, 0)]  
    if p.degree() == 1:  
        return [(p.coef[0], p.coef[1], 0)]  
    if p.degree() == 2:  
        return [tuple(p.coef)]  
    odd = Polynomial(p.coef[1::2])  
    roots = odd.roots()  
    a = next((r for r in roots if np.isreal(r)), None)  
    if a is None:  
        raise NoRealRoots  
    q, r = divmod(p, Polynomial([-a, 0, 1]))  
    rec = ke(q)  
    return rec + [(r(0), a)]  
  
  
def knuth(pol):  
    shift = 1 / 10  
    while True:  
        p_shifted = pol(Polynomial([shift, 1]))  
        try:  
            prog = ke(p_shifted)  
        except NoRealRoots:  
            shift *= 2  
            continue  
        return shift, prog  
  
  
class KnuthEve:  
    def __init__(self, pol):  
        self.shift, self.prog = knuth(pol)  
  
    def __call__(self, x):  
        x -= self.shift  
        x2 = x**2  
        b0, a0, c0 = self.prog[0]  
        res = b0 + a0 * x + c0 * x2  
        for b, a in self.prog[1:]:  
            res = (x2 - a) * res + b  
        return res  
  
    def __repr__(self):  
        res = [f"KnuthEve(shift={self.shift}, prog="]  
        res.append(f'    y := x*x')  
        b0, a0, c0 = self.prog[0]  
        if c0 != 0:  
            res.append(f'    r := {b0:.3} + {a0:.3} x + {c0:.3} y')  
        else:  
            res.append(f'    r := {b0:.3} + {a0:.3} x')  
        for b, a in self.prog[1:]:  
            if a >= 0:  
                res.append(f'    r := {b:.3} + (y - {a:.3}) r')  
            else:  
                res.append(f'    r := {b:.3} + (y + {-a:.3}) r')  
        res.append(')')  
        return '\n'.join(res)  
  
  
exp_pol = Polynomial([1 / fac(n) for n in range(8)])  
print('Input pol', exp_pol)  
ke = KnuthEve(exp_pol)  
print(ke)  
  
for x in np.random.randn(3):  
    print(f"x={x=}, {exp_pol(x)=}, {ke(x)=}")  
```  
  
Here's the output:  
```  
Input pol 1.0 + 1.0·x¹ + 0.5·x² + 0.16666666666666666·x³ + 0.041666666666666664·x⁴ +  
0.008333333333333333·x⁵ + 0.001388888888888889·x⁶ +  
0.0001984126984126984·x⁷  
KnuthEve(shift=0.8, prog=  
    y := x*x  
    r := 0.0025 + 0.000198 x  
    r := -0.131 + (y + 11.5) r  
    r := 6.58 + (y + 15.8) r  
    r := -3.05e+02 + (y + 61.7) r  
)  
x=x=0.2330739187422812, exp_pol(x)=1.2624747962056768, ke(x)=1.2624747962055949  
x=x=-0.9801588688418243, exp_pol(x)=0.37523244467527916, ke(x)=0.37523244467519135  
x=x=-0.32536824752745475, exp_pol(x)=0.722261330708069, ke(x)=0.7222613307080223  
```  
  
It seems reasonably numerically stable, but you should play around with some polynomials for yourself.  
There's also the Rabin-Winograd method, which is like a mix between Estrin's method and Knuth Eve, in that it uses n/2 + logn multiplications.

---

## Comment 24

> Username: NAThompson  
> Created at: 2023-01-27 19:17:27 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1406968030  

@thomasahle : This is awesome.  
  
At this point I believe that the first course of action is to create an MR with `boost/math/tools/estrin.hpp`. I'd template it on a `RandomAccessContainer`, and have a specialization for `std::array` so that there is no allocation in that important case.  
  
@jzmaddock , @mborland : Sound sensible?

---

## Comment 25

> Username: mborland  
> Created at: 2023-01-27 19:21:00 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1406971194  

>   
  
I think so. If we are really trying to avoid allocation the user could pass a pointer to pre-allocated space so it's independent of `std::array`.

---

## Comment 26

> Username: thomasahle  
> Created at: 2023-01-28 00:26:34 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1407216436  

If we are using estrin for very large polynomials, possibly unbounded, it's probably best to just use it block-wise and combine the blocks with normal horner (using a sufficiently larger power of x).

---

## Comment 27

> Username: NAThompson  
> Created at: 2023-01-28 17:36:14 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1407447189  

@thomasahle : I was kinda in a hurry to get this in, so I just created a pull request for this [here](https://github.com/boostorg/math/pull/932).  
  
Obviously feel free to make PRs to the branch or otherwise add commentary!

---

## Comment 28

> Username: jzmaddock  
> Created at: 2023-01-28 18:52:39 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1407462862  

Some random comments:  
  
@NAThompson the line `std::fill(c.begin(), c.end(), unif(mt));` in your test code doesn't do what you think (it sets all the coefficients to the same value).  
  
@thomasahle : The variable length array in your estrin code is a GCC-ism only, it's not part of C++ at all (though I wish it was!), I was also getting buffer-overrun errors from using `RealOrComplex ds[(n+1)/2];`, changing to `RealOrComplex ds[(n+1)/2 + 1];` fixed that, or maybe it should be `RealOrComplex ds[n/2 + 1];`  ??  
  
In any case I've been playing around with some test code comparing to our existing code, here's what I'm using:  
  
```  
//  (C) Copyright John Maddock 2023.  
//  Use, modification and distribution are subject to the  
//  Boost Software License, Version 1.0. (See accompanying file  
//  LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
#include <random>  
#include <array>  
#include <vector>  
#include <boost/math/tools/rational.hpp>  
#include <benchmark/benchmark.h>  
  
template<class Real, std::size_t N>  
void existing_horners_method(benchmark::State& state)  
{  
   std::mt19937_64 mt(0);  
   std::uniform_real_distribution<Real> unif(-10, 10);  
  
   Real c[N];  
   for (std::size_t i = 0; i < N; ++i)  
      c[i] = unif(mt);  
   Real x = unif(mt);  
   Real fudge = std::sqrt(std::numeric_limits<Real>::epsilon());  
   for (auto _ : state)  
   {  
      Real output = boost::math::tools::evaluate_polynomial(c, x);  
      benchmark::DoNotOptimize(output);  
      x += fudge;  
   }  
}  
  
template<typename Real, std::size_t N>  
inline auto estrin_fixed(const Real (&coeffs)[N], Real z) {  
   if (N == 1)  
      return coeffs[0];  
  
   Real ds[(N + 1) / 2 + 1];  
   for (int i = 0; i < N / 2; i++) {  
      ds[i] = coeffs[2 * i] + coeffs[2 * i + 1] * z;  
   }  
   if ((N & 1) == 1) ds[N / 2] = coeffs[N - 1];  
   std::size_t n = (N + 1) / 2;  
  
   while (n != 1) {  
      z = z * z;  
      for (int i = 0; i < n / 2; i++) {  
         ds[i] = ds[2 * i] + ds[2 * i + 1] * z;  
      }  
      if ((n & 1) == 1) ds[n / 2] = ds[n - 1];  
      n = (n + 1) / 2;  
   }  
  
   return ds[0];  
}  
  
template<class Real, std::size_t N>  
void estrin_method(benchmark::State& state)  
{  
   std::mt19937_64 mt(0);  
   std::uniform_real_distribution<Real> unif(-10, 10);  
  
   Real c[N];  
   for (std::size_t i = 0; i < N; ++i)  
      c[i] = unif(mt);  
   Real x = unif(mt);  
   Real fudge = std::sqrt(std::numeric_limits<Real>::epsilon());  
   for (auto _ : state)  
   {  
      Real output = estrin_fixed(c, x);  
      benchmark::DoNotOptimize(output);  
      x += fudge;  
   }  
}  
  
template<class Real>  
void existing_horners_method_variable(benchmark::State& state)  
{  
   long n = state.range(0);  
   std::mt19937_64 mt(0);  
   std::uniform_real_distribution<Real> unif(-10, 10);  
  
   std::vector<Real> c;  
   for (std::size_t i = 0; i < n; ++i)  
      c.push_back(unif(mt));  
   Real x = unif(mt);  
   Real fudge = std::sqrt(std::numeric_limits<Real>::epsilon());  
   for (auto _ : state)  
   {  
      Real output = boost::math::tools::evaluate_polynomial(c.data(), x, n);  
      benchmark::DoNotOptimize(output);  
      x += fudge;  
   }  
   state.SetComplexityN(state.range(0));  
}  
  
template<typename Real>  
inline auto estrin_variable(const Real* coeffs, Real z, std::size_t N, Real* ds) {  
   if (N == 1)  
      return coeffs[0];  
  
   for (int i = 0; i < N / 2; i++) {  
      ds[i] = coeffs[2 * i] + coeffs[2 * i + 1] * z;  
   }  
   if ((N & 1) == 1) ds[N / 2] = coeffs[N - 1];  
   std::size_t n = (N + 1) / 2;  
  
   while (n != 1) {  
      z = z * z;  
      for (int i = 0; i < n / 2; i++) {  
         ds[i] = ds[2 * i] + ds[2 * i + 1] * z;  
      }  
      if ((n & 1) == 1) ds[n / 2] = ds[n - 1];  
      n = (n + 1) / 2;  
   }  
  
   return ds[0];  
}  
  
template<class Real>  
void estrin_method_variable(benchmark::State& state)  
{  
   long n = state.range(0);  
   std::mt19937_64 mt(0);  
   std::uniform_real_distribution<Real> unif(-10, 10);  
  
   std::vector<Real> c, d(n);  
   for (std::size_t i = 0; i < n; ++i)  
      c.push_back(unif(mt));  
  
   Real x = unif(mt);  
   Real fudge = std::sqrt(std::numeric_limits<Real>::epsilon());  
   for (auto _ : state)  
   {  
      Real output = estrin_variable(c.data(), x, n, d.data());  
      benchmark::DoNotOptimize(output);  
      x += fudge;  
   }  
   state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(existing_horners_method, double, 3);  
BENCHMARK_TEMPLATE(existing_horners_method, double, 4);  
BENCHMARK_TEMPLATE(existing_horners_method, double, 5);  
BENCHMARK_TEMPLATE(existing_horners_method, double, 5);  
BENCHMARK_TEMPLATE(existing_horners_method, double, 6);  
BENCHMARK_TEMPLATE(existing_horners_method, double, 7);  
BENCHMARK_TEMPLATE(existing_horners_method, double, 8);  
BENCHMARK_TEMPLATE(existing_horners_method, double, 9);  
BENCHMARK_TEMPLATE(existing_horners_method, double, 10);  
BENCHMARK_TEMPLATE(existing_horners_method, double, 11);  
BENCHMARK_TEMPLATE(existing_horners_method, double, 12);  
BENCHMARK_TEMPLATE(existing_horners_method, double, 13);  
BENCHMARK_TEMPLATE(existing_horners_method, double, 14);  
BENCHMARK_TEMPLATE(existing_horners_method, double, 15);  
  
BENCHMARK_TEMPLATE(estrin_method, double, 3);  
BENCHMARK_TEMPLATE(estrin_method, double, 4);  
BENCHMARK_TEMPLATE(estrin_method, double, 5);  
BENCHMARK_TEMPLATE(estrin_method, double, 5);  
BENCHMARK_TEMPLATE(estrin_method, double, 6);  
BENCHMARK_TEMPLATE(estrin_method, double, 7);  
BENCHMARK_TEMPLATE(estrin_method, double, 8);  
BENCHMARK_TEMPLATE(estrin_method, double, 9);  
BENCHMARK_TEMPLATE(estrin_method, double, 10);  
BENCHMARK_TEMPLATE(estrin_method, double, 11);  
BENCHMARK_TEMPLATE(estrin_method, double, 12);  
BENCHMARK_TEMPLATE(estrin_method, double, 13);  
BENCHMARK_TEMPLATE(estrin_method, double, 14);  
BENCHMARK_TEMPLATE(estrin_method, double, 15);  
  
BENCHMARK_TEMPLATE(existing_horners_method_variable, double)->Range(1, 1 << 15)->Complexity();  
BENCHMARK_TEMPLATE(estrin_method_variable, double)->Range(1, 1 << 15)->Complexity();  
  
  
  
BENCHMARK_MAIN();  
```  
  
Results with msvc were basically meaningless - same time for every test - I suspect over-optimisation?  gcc-cygwin did much better:  
  
```  
existing_horners_method<double, 3>                   1.16 ns         1.14 ns    560000000  
existing_horners_method<double, 4>                   1.36 ns         1.35 ns    497777778  
existing_horners_method<double, 5>                   1.70 ns         1.74 ns    448000000  
existing_horners_method<double, 5>                   1.70 ns         1.74 ns    448000000  
existing_horners_method<double, 6>                   1.85 ns         1.84 ns    407272727  
existing_horners_method<double, 7>                   2.08 ns         1.93 ns    373333333  
existing_horners_method<double, 8>                   2.37 ns         2.13 ns    344615385  
existing_horners_method<double, 9>                   2.56 ns         2.57 ns    280000000  
existing_horners_method<double, 10>                  2.92 ns         2.92 ns    235789474  
existing_horners_method<double, 11>                  3.17 ns         2.98 ns    235789474  
existing_horners_method<double, 12>                  3.67 ns         3.37 ns    213333333  
existing_horners_method<double, 13>                  3.91 ns         3.84 ns    179200000  
existing_horners_method<double, 14>                  4.52 ns         4.35 ns    172307692  
existing_horners_method<double, 15>                  4.91 ns         4.87 ns    144516129  
estrin_method<double, 3>                             1.41 ns         1.41 ns    497777778  
estrin_method<double, 4>                             1.62 ns         1.60 ns    448000000  
estrin_method<double, 5>                             1.84 ns         1.80 ns    373333333  
estrin_method<double, 5>                             1.85 ns         1.76 ns    407272727  
estrin_method<double, 6>                             2.02 ns         2.01 ns    373333333  
estrin_method<double, 7>                             2.32 ns         2.34 ns    320000000  
estrin_method<double, 8>                             2.49 ns         2.37 ns    263529412  
estrin_method<double, 9>                             2.87 ns         2.76 ns    248888889  
estrin_method<double, 10>                            3.16 ns         3.14 ns    224000000  
estrin_method<double, 11>                            3.64 ns         3.52 ns    186666667  
estrin_method<double, 12>                            3.11 ns         2.93 ns    224000000  
estrin_method<double, 13>                            3.66 ns         3.69 ns    194782609  
estrin_method<double, 14>                            3.77 ns         3.75 ns    179200000  
estrin_method<double, 15>                            11.2 ns         10.9 ns     74666667  
existing_horners_method_variable<double>/1           1.05 ns         1.00 ns    746666667  
existing_horners_method_variable<double>/8           4.32 ns         3.92 ns    179200000  
existing_horners_method_variable<double>/64          76.4 ns         76.7 ns      8960000  
existing_horners_method_variable<double>/512          993 ns         1001 ns       640000  
existing_horners_method_variable<double>/4096        8270 ns         8196 ns        89600  
existing_horners_method_variable<double>/32768      66249 ns        65569 ns        11200  
existing_horners_method_variable<double>_BigO        2.02 N          2.00 N  
existing_horners_method_variable<double>_RMS            0 %             0 %  
estrin_method_variable<double>/1                     1.01 ns        0.994 ns    896000000  
estrin_method_variable<double>/8                     5.90 ns         5.86 ns    112000000  
estrin_method_variable<double>/64                    29.4 ns         29.2 ns     23578947  
estrin_method_variable<double>/512                    237 ns          235 ns      2986667  
estrin_method_variable<double>/4096                  1987 ns         1918 ns       407273  
estrin_method_variable<double>/32768                17165 ns        17090 ns        44800  
estrin_method_variable<double>_BigO                  0.52 N          0.52 N  
estrin_method_variable<double>_RMS                      2 %             3 %  
```  
  
This is actually quite interesting: estrin is quite a bit slower for small fixed size arrays (the sort of thing we use for special function evaluation for example), but then starts to speed ahead for larger polynomials with a big difference at 32K size.  I know I'm pretty unlikely to ever use polynomials that big, but I can't speak for @NAThompson ;)  
  
BTW I should add that our existing fixed-size polynomial code may well have an unfair advantage: they're loop unrolled and carefully coded to allow for parallel execution via a second order Horner scheme.  It might be interesting to do something similar with small-order estrin, or else for estrin to delegate to Horner for smaller sizes.

---

## Comment 29

> Username: NAThompson  
> Created at: 2023-01-28 18:58:38 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1407463824  

@jzmaddock : Fixing the issues you found in the pull request; should we begin migrating the discussion over there?

---

## Comment 30

> Username: jzmaddock  
> Created at: 2023-01-28 19:00:16 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1407464181  

@NAThompson the buffer length issue may effect your PR as well - or it may be a false alarm from msvc's static analysis - but basically it actually refused to compile the (N+1)/2 sized buffer at all on the grounds that it would overflow - first time I've seen that kind of compiler error!

---

## Comment 31

> Username: NAThompson  
> Created at: 2023-01-28 19:05:51 UTC  
> Updated at: 2023-01-28 19:16:14 UTC  
> Url: https://github.com/boostorg/math/issues/924#issuecomment-1407465449  

@jzmaddock : Yeah, I just changed that line to be more "boosty"-basically it gets allocated in a `std::vector` if you don't provide scratch space.  
  
  
As to the buffer overflow: I ran it under address sanitizer and it was fine . . .
