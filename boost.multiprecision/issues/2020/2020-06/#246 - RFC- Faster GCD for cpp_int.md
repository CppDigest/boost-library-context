# #246 - RFC: Faster GCD for cpp_int [Closed]

> Username: jzmaddock  
> Created at: 2020-06-09 12:55:05 UTC  
> Updated at: 2020-06-19 19:11:09 UTC  
> Closed at: 2020-06-19 19:11:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/246  

I'm not opening a PR for this yet, as the code isn't ready, just a place to report back experiments with Lehmer's algorithm.  
  
Here's the improvements I see with msvc, figures are for CPU time, normalised to the GMP values:  
  
  
Bits |   | GMP | develop | Euclid | Lehman  
-- | -- | -- | -- | -- | --  
  |   |   |   |   |    
1024 |   | 10253906 | 3.8496241335 | 7.4550420103 | 3.8921976659  
2048 |   | 24553571 | 4.6666667345 | 7.4789579487 | 4.0624250949  
4096 |   | 58238636 | 6.5731707727 | 7.8154165561 | 4.0352244342  
8192 |   | 156250000 | 8.5 | 7.484832 | 4.04244416  
16384 |   | 476562500 | 10.8196721311 | 7.3225218098 | 3.5963358426  
32768 |   | 2250000000 | 8.4097222222 | 5.0483443111 | 2.4772322667  
65536 |   | 5515625000 | 13.88101983 | 7.14713377 | 3.5962916079  
  
Again for GCC/Minw64:  
  
  
Bits |   | GMP | develop | Euclid | Lehman  
-- | -- | -- | -- | -- | --  
  |   |   |   |   |    
1024 |   | 6786923 | 3.965184812 | 11.7793176672 | 3.0818895396  
2048 |   | 14968472 | 5.2147874546 | 12.4039263994 | 3.3624961853  
4096 |   | 33747355 | 7.4381424263 | 13.2068972517 | 4.1528906488  
8192 |   | 79889889 | 10.9957844603 | 14.9052266176 | 4.2267582823  
16384 |   | 202028225 | 16.1484431198 | 16.8925757775 | 4.3667428153  
32768 |   | 604895700 | 20.6833401196 | 18.2614816075 | 4.4473010802  
65536 |   | 1599059800 | 30.488035657 | 24.9671799641 | 5.5443492482  
  
Which just goes to show how bad the current routine is!  
  
Code attached.  
  
TODO:  
  
* We should use double_limb arithmetic inside lehmer to eliminate a full limb per main loop instead of half a limb.  
* Lehmer needs to be made to work with unsigned types.  
* I need to check that Lehmer doesn't rely on UB as there's some mixed signed/unsigned arithmetic :(  
* Currently Lehmer always performs a Euclid step in addition to the Lehmer step.... in the paper I used this was added to make performance analysis easier, but I don't *think*  it's required.  
* A core step inside Lehmer is multiplication by a single limb followed by a subtraction - we have optimised the latter but not the former via intrinsics.  
* Other algorithms?  Knuth hints at applying a Lehmer type routine to the binary GCD for example, there are also K-ary binary GCD's.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-06-09 12:55:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/246#issuecomment-641272465  

Code:  
  
```  
  
#include <iostream>  
#include <benchmark/benchmark.h>  
#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/multiprecision/gmp.hpp>  
#include <boost/random.hpp>  
#include <cmath>  
  
#include <immintrin.h>  
  
using namespace boost::multiprecision;  
using namespace boost::random;  
  
namespace boost {  
   namespace multiprecision {  
      namespace backends {  
  
         template <class CppInt>  
         void eval_gcd_lehmer(CppInt& U, CppInt& V, unsigned lu)  
         {  
            unsigned h = lu % bits_per_limb;  
            limb_type u = U.limbs()[U.size() - 1];  
            limb_type v = V.size() < U.size() ? 0 : V.limbs()[V.size() - 1];  
            if (h)  
            {  
               u <<= bits_per_limb - h;  
               u |= U.limbs()[U.size() - 2] >> h;  
               v <<= bits_per_limb - h;  
               v |= V.limbs()[U.size() - 2] >> h;  
            }  
  
            signed_limb_type x[3] = { 1, 0 };  
            signed_limb_type y[3] = { 0, 1 };  
            unsigned i = 0;  
            bool done = false;  
  
            while (true)  
            {  
               limb_type q = u / v;  
               x[2] = x[0] - q * x[1];  
               y[2] = y[0] - q * y[1];  
               limb_type tu = u;  
               u = v;  
               v = tu - q * v;  
               ++i;  
               if ((i & 1u) == 0)  
               {  
                  assert(u > v);  
                  done = (v < -x[2]) || ((u - v) < (y[2] - y[1]));  
               }  
               else  
               {  
                  assert(u > v);  
                  done = (v < -y[2]) || ((u - v) < (x[2] - x[1]));  
               }  
               if (done)  
               {  
                  if (i == 1)  
                  {  
                     // No change to U and V we've stalled!  
                     CppInt t;  
                     eval_modulus(t, U, V);  
                     U.swap(V);  
                     V.swap(t);  
                     return;  
                  }  
                  CppInt t1, t2, tu;  
                  assert((x[0] < 0) != (y[0] < 0));  
                  assert((x[1] < 0) != (y[1] < 0));  
                  eval_multiply(t1, U, x[0]);  
                  eval_multiply(t2, V, y[0]);  
                  eval_add(tu, t1, t2);  
                  eval_multiply(t1, U, x[1]);  
                  eval_multiply(t2, V, y[1]);  
                  eval_add(V, t1, t2);  
                  eval_modulus(U, tu, V);  
                  U.swap(V);  
                  assert(lu > eval_msb(U));  
                  return;  
               }  
               x[0] = x[1];  
               x[1] = x[2];  
               y[0] = y[1];  
               y[1] = y[2];  
            }  
         }  
  
           
         template <class CppInt>  
         void eval_gcd_basic_lehman(CppInt& result, const CppInt& a, const CppInt& b)  
         {  
            using default_ops::eval_get_sign;  
            using default_ops::eval_is_zero;  
            using default_ops::eval_lsb;  
  
            if (a.size() == 1)  
            {  
               eval_gcd(result, b, *a.limbs());  
               return;  
            }  
            if (b.size() == 1)  
            {  
               eval_gcd(result, a, *b.limbs());  
               return;  
            }  
  
            CppInt U(a), V(b);  
  
            int s = eval_get_sign(U);  
  
            /* GCD(0,x) := x */  
            if (s < 0)  
            {  
               U.negate();  
            }  
            else if (s == 0)  
            {  
               result = V;  
               return;  
            }  
            s = eval_get_sign(V);  
            if (s < 0)  
            {  
               V.negate();  
            }  
            else if (s == 0)  
            {  
               result = U;  
               return;  
            }  
  
            if (U.compare(V) < 0)  
               U.swap(V);  
  
            while (!eval_is_zero(V))  
            {  
               if (V.size() <= 2)  
               {  
                  //  
                  // Special case: if V has no more than 2 limbs  
                  // then we can reduce U and V to a pair of integers and perform  
                  // direct integer gcd:  
                  //  
                  if (V.size() == 1)  
                     U = eval_gcd(*V.limbs(), *U.limbs());  
                  else  
                  {  
                     double_limb_type i = V.limbs()[0] | (static_cast<double_limb_type>(V.limbs()[1]) << sizeof(limb_type) * CHAR_BIT);  
                     double_limb_type j = (U.size() == 1) ? *U.limbs() : U.limbs()[0] | (static_cast<double_limb_type>(U.limbs()[1]) << sizeof(limb_type) * CHAR_BIT);  
                     U = eval_gcd(i, j);  
                  }  
                  break;  
               }  
               unsigned lu = eval_msb(U) + 1;  
               unsigned lv = eval_msb(V) + 1;  
               if (lu - lv <= bits_per_limb / 2)  
               {  
                  eval_gcd_lehmer(U, V, lu);  
               }  
               else  
               {  
                  eval_modulus(result, U, V);  
                  U.swap(V);  
                  V.swap(result);  
               }  
            }  
            result.swap(U);  
         }  
           
         template <class CppInt>  
         void eval_gcd_basic_euclid(CppInt& result, const CppInt& a, const CppInt& b)  
         {  
            using default_ops::eval_get_sign;  
            using default_ops::eval_is_zero;  
            using default_ops::eval_lsb;  
  
            if (a.size() == 1)  
            {  
               eval_gcd(result, b, *a.limbs());  
               return;  
            }  
            if (b.size() == 1)  
            {  
               eval_gcd(result, a, *b.limbs());  
               return;  
            }  
  
            CppInt U(a), V(b);  
  
            int s = eval_get_sign(U);  
  
            /* GCD(0,x) := x */  
            if (s < 0)  
            {  
               U.negate();  
            }  
            else if (s == 0)  
            {  
               result = V;  
               return;  
            }  
            s = eval_get_sign(V);  
            if (s < 0)  
            {  
               V.negate();  
            }  
            else if (s == 0)  
            {  
               result = U;  
               return;  
            }  
  
            if (U.compare(V) < 0)  
               U.swap(V);  
  
            while (!eval_is_zero(V))  
            {  
               if (V.size() <= 2)  
               {  
                  //  
                  // Special case: if V has no more than 2 limbs  
                  // then we can reduce U and V to a pair of integers and perform  
                  // direct integer gcd:  
                  //  
                  if (V.size() == 1)  
                     U = eval_gcd(*V.limbs(), *U.limbs());  
                  else  
                  {  
                     double_limb_type i = V.limbs()[0] | (static_cast<double_limb_type>(V.limbs()[1]) << sizeof(limb_type) * CHAR_BIT);  
                     double_limb_type j = (U.size() == 1) ? *U.limbs() : U.limbs()[0] | (static_cast<double_limb_type>(U.limbs()[1]) << sizeof(limb_type) * CHAR_BIT);  
                     U = eval_gcd(i, j);  
                  }  
                  break;  
               }  
               eval_modulus(result, U, V);  
               U.swap(V);  
               V.swap(result);  
            }  
            result.swap(U);  
         }  
           
  
      }  
   }  
}  
  
template <class T>  
std::tuple<std::vector<T>, std::vector<T>, std::vector<T> >& get_test_vector(unsigned bits)  
{  
   static std::map<unsigned, std::tuple<std::vector<T>, std::vector<T>, std::vector<T> > > data;  
  
   std::tuple<std::vector<T>, std::vector<T>, std::vector<T> >& result = data[bits];  
  
   if (std::get<0>(result).size() == 0)  
   {  
      mt19937                     mt;  
      uniform_int_distribution<T> ui(T(1) << (bits - 1), T(1) << bits);  
  
      std::vector<T>& a = std::get<0>(result);   
      std::vector<T>& b = std::get<1>(result);  
      std::vector<T>& c = std::get<2>(result);  
  
      for (unsigned i = 0; i < 1000; ++i)  
      {  
         a.push_back(ui(mt));  
         b.push_back(ui(mt));  
         if (b.back() > a.back())  
            b.back().swap(a.back());  
         c.push_back(0);  
      }  
   }  
   return result;  
}  
  
template <class T>  
std::vector<T>& get_test_vector_a(unsigned bits)  
{  
   return std::get<0>(get_test_vector<T>(bits));  
}  
template <class T>  
std::vector<T>& get_test_vector_b(unsigned bits)  
{  
   return std::get<1>(get_test_vector<T>(bits));  
}  
template <class T>  
std::vector<T>& get_test_vector_c(unsigned bits)  
{  
   return std::get<2>(get_test_vector<T>(bits));  
}  
  
  
template <typename T>  
static void BM_gcd_old(benchmark::State& state)  
{  
   int                         bits = state.range(0);  
  
   std::vector<T>& a = get_test_vector_a<T>(bits);  
   std::vector<T>& b = get_test_vector_b<T>(bits);  
   std::vector<T>& c = get_test_vector_c<T>(bits);  
  
   for (auto _ : state)  
   {  
      for (unsigned i = 0; i < a.size(); ++i)  
         eval_gcd(c[i].backend(), a[i].backend(), b[i].backend());  
   }  
}  
  
template <typename T>  
static void BM_gcd_lehman(benchmark::State& state)  
{  
   int                         bits = state.range(0);  
  
   std::vector<T>& a = get_test_vector_a<T>(bits);  
   std::vector<T>& b = get_test_vector_b<T>(bits);  
   std::vector<T>& c = get_test_vector_c<T>(bits);  
  
   for (auto _ : state)  
   {  
      for (unsigned i = 0; i < a.size(); ++i)  
         eval_gcd_basic_lehman(c[i].backend(), a[i].backend(), b[i].backend());  
   }  
}  
template <typename T>  
static void BM_gcd_euclid(benchmark::State& state)  
{  
   int                         bits = state.range(0);  
  
   std::vector<T>& a = get_test_vector_a<T>(bits);  
   std::vector<T>& b = get_test_vector_b<T>(bits);  
   std::vector<T>& c = get_test_vector_c<T>(bits);  
  
   for (auto _ : state)  
   {  
      for (unsigned i = 0; i < a.size(); ++i)  
         eval_gcd_basic_euclid(c[i].backend(), a[i].backend(), b[i].backend());  
   }  
}  
  
  
  
BENCHMARK_TEMPLATE(BM_gcd_old, mpz_int)->RangeMultiplier(2)->Range(1024, 1 << 16);  
BENCHMARK_TEMPLATE(BM_gcd_old, cpp_int)->RangeMultiplier(2)->Range(1024, 1 << 16);  
BENCHMARK_TEMPLATE(BM_gcd_lehman, cpp_int)->RangeMultiplier(2)->Range(1024, 1 << 16);  
BENCHMARK_TEMPLATE(BM_gcd_euclid, cpp_int)->RangeMultiplier(2)->Range(1024, 1 << 16);  
  
BENCHMARK_MAIN();  
```

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-06-09 13:10:25 UTC  
> Updated at: 2020-06-09 13:16:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/246#issuecomment-641280383  

@jzmaddock : Why not a PR marked as draft?  
  
Also the GMP version is orders of magnitude slower? Or is that a units problem?  
  
Edit: I see the problem now. Try this:  
  
```cpp  
BENCHMARK_TEMPLATE(BM_gcd_old, mpz_int)->RangeMultiplier(2)->Range(1024, 1 << 16)->Unit(benchmark::kMillisecond);  
```

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-06-09 13:16:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/246#issuecomment-641288189  

>Why not a PR marked as draft?  
  
I haven't actually changed any code yet - everything is in the benchmark test.  
  
> Also the GMP version is orders of magnitude slower? Or is that a units problem?  
  
Sorry I wasn't clearer: the GMP column contains the actual benchmark value in ns, the others are relative to that value, so the GMP results always score 1 on the relative scale.

---

## Comment 4

> Username: NAThompson  
> Created at: 2020-06-09 13:18:20 UTC  
> Updated at: 2020-06-09 13:20:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/246#issuecomment-641289979  

@jzmaddock : I'm now sad that I didn't make these google benchmark files part of the `reporting` parts of the repo; I think starting a PR by writing the benchmark is a good way to go. Performance driven development.  
  
> Sorry I wasn't clearer  
  
Actually you were, I just didn't read it carefully. It's a nice way to communicate the data.

---

## Comment 5

> Username: madhur4127  
> Created at: 2020-06-09 15:07:22 UTC  
> Updated at: 2020-06-09 16:10:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/246#issuecomment-641358619  

This is a great improvement, John.  
  
One interesting thing is the growth of the constant factor with respect to bits. I think this hints that GMP is switching to something much faster.  
  
Well, I will try to find a good implementation for the single limb mul. ~~But I observed that although using 128bit integer we can processes double the amount of data in a single pass, but the compiler can very well optimize up to a 64-bit integer. I mean that processing 32 bits generated fast assembly code and very efficient loop.~~  
I was wrong. Here's the benchmark: http://quick-bench.com/-hxukCgfHk4AO7DtIgjcxQ5zhXo  
```c++  
//using big = unsigned __int128;  
//using small = unsigned long long;  
  
using big = unsigned long long;  
using small = unsigned;  
  
void mul(small *pa, small *pr, small m, unsigned n){  
    big prev = 0;  
    for(int i=0; i<n; ++i, prev >>= sizeof(small)*8){  
        big temp = pa[i] * (big) m;  
        pr[i]=(prev+=temp);  
    }  
    pr[n]=prev;  
}  
```

---

## Comment 6

> Username: jzmaddock  
> Created at: 2020-06-09 18:19:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/246#issuecomment-641488801  

>One interesting thing is the growth of the constant factor with respect to bits. I think this hints that GMP is switching to something much faster.  
  
I'm not sure - I don't *think* it does, but I could be wrong.  
  
> Well, I will try to find a good implementation for the single limb mul.  
  
We already have a single limb multiply similar to your test code (thanks for that), in principle though it's a single mulx followed by an addc per loop, or perhaps there are better instructions/intrinsics?  I confess I find Intel's docs mighty confusing at times :(

---

## Comment 7

> Username: jzmaddock  
> Created at: 2020-06-19 19:11:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/246#issuecomment-646822420  

There's now a version that's good enough for a PR: https://github.com/boostorg/multiprecision/pull/250
