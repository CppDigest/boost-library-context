# #234 Faster addition using Intel intrinsics. [Merged]

> Username: jzmaddock  
> Created at: 2020-05-12 21:00:57 UTC  
> Updated at: 2020-06-02 18:28:47 UTC  
> Merged at: 2020-05-28 12:35:08 UTC  
> Closed at: 2020-05-28 12:35:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234  

Replaces: https://github.com/boostorg/multiprecision/pull/233.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-05-13 12:23:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-627946282  

Just discovered (while looking at subtraction) that there's a subtle issue with the benchmarks we were using that meant we were testing non-random values.  I need to re-evaluate the benchmarks just as a double check...

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-05-13 17:23:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-628133346  

Benchmarks in PR's prior to this one were generating test values which were mostly zero (so not carry!).  
  
Updated MSVC test results:  
  
```  
BM_add_old<cpp_int>/1024            73160 ns        71150 ns        11200  
BM_add_old<cpp_int>/2048           115318 ns       114397 ns         5600  
BM_add_old<cpp_int>/4096           171206 ns       167426 ns         3733  
BM_add_old<cpp_int>/8192           310536 ns       307603 ns         2489  
BM_add_old<cpp_int>/16384          726716 ns       739397 ns         1120  
BM_add_old<cpp_int>/32768         1161796 ns      1143973 ns          560  
BM_add_old<cpp_int>/65536         1950260 ns      1968834 ns          373  
BM_add_new<cpp_int>/1024            30711 ns        31145 ns        23579  
BM_add_new<cpp_int>/2048            47348 ns        48652 ns        14452  
BM_add_new<cpp_int>/4096            80554 ns        81961 ns         8960  
BM_add_new<cpp_int>/8192           151841 ns       149972 ns         4480  
BM_add_new<cpp_int>/16384          326254 ns       329641 ns         2133  
BM_add_new<cpp_int>/32768          769317 ns       784738 ns          896  
BM_add_new<cpp_int>/65536         1552230 ns      1569475 ns          448  
BM_add_new_64<cpp_int>/1024         22431 ns        21973 ns        32000  
BM_add_new_64<cpp_int>/2048         36448 ns        36622 ns        21333  
BM_add_new_64<cpp_int>/4096         52126 ns        51563 ns        10000  
BM_add_new_64<cpp_int>/8192         90180 ns        89979 ns         7467  
BM_add_new_64<cpp_int>/16384       220225 ns       219727 ns         3200  
BM_add_new_64<cpp_int>/32768       664631 ns       662667 ns          896  
BM_add_new_64<cpp_int>/65536      1439122 ns      1429967 ns          448  
```  
The BM_add_new_64 tests do 64-bit adds even though the limbs are 32 bit - so there is a slight but probably worthwhile improvement.  
  
Updated benchmark:  
  
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
  
  
         template <class CppInt1, class CppInt2, class CppInt3>  
         inline BOOST_MP_CXX14_CONSTEXPR void add_unsigned_64(CppInt1& result, const CppInt2& a, const CppInt3& b) BOOST_MP_NOEXCEPT_IF(is_non_throwing_cpp_int<CppInt1>::value)  
         {  
#ifndef BOOST_MP_NO_CONSTEXPR_DETECTION  
            if (BOOST_MP_IS_CONST_EVALUATED(a.size()))  
            {  
               add_unsigned_constexpr(result, a, b);  
            }  
            else  
#endif  
            {  
               using ::boost::multiprecision::std_constexpr::swap;  
  
               // Nothing fancy, just let uintmax_t take the strain:  
               unsigned m(0), x(0);  
               unsigned as = a.size();  
               unsigned bs = b.size();  
               minmax(as, bs, m, x);  
               if (x == 1)  
               {  
                  bool s = a.sign();  
                  result = static_cast<double_limb_type>(*a.limbs()) + static_cast<double_limb_type>(*b.limbs());  
                  result.sign(s);  
                  return;  
               }  
               result.resize(x, x);  
               typename CppInt2::const_limb_pointer pa = a.limbs();  
               typename CppInt3::const_limb_pointer pb = b.limbs();  
               typename CppInt1::limb_pointer       pr = result.limbs();  
               //typename CppInt1::limb_pointer       pr_end = pr + m;  
  
               if (as < bs)  
                  swap(pa, pb);  
               // First where a and b overlap:  
               unsigned      i = 0;  
               unsigned char carry = 0;  
               for (; i + 8 <= m; i += 8)  
               {  
                  carry = _addcarry_u64(carry, *(unsigned long long*)(pa + i + 0), *(unsigned long long*)(pb + i + 0), (unsigned long long*)(pr + i));  
                  carry = _addcarry_u64(carry, *(unsigned long long*)(pa + i + 2), *(unsigned long long*)(pb + i + 2), (unsigned long long*)(pr + i + 2));  
                  carry = _addcarry_u64(carry, *(unsigned long long*)(pa + i + 4), *(unsigned long long*)(pb + i + 4), (unsigned long long*)(pr + i + 4));  
                  carry = _addcarry_u64(carry, *(unsigned long long*)(pa + i + 6), *(unsigned long long*)(pb + i + 6), (unsigned long long*)(pr + i + 6));  
               }  
               for (; i < m; ++i)  
                  carry = ::boost::multiprecision::detail::addcarry_limb(carry, pa[i], pb[i], pr + i);  
               for (; i < x && carry; ++i)  
                  carry = ::boost::multiprecision::detail::addcarry_limb(carry, pa[i], 0, pr + i);  
               if (i == x && carry)  
               {  
                  // We overflowed, need to add one more limb:  
                  result.resize(x + 1, x + 1);  
                  if (result.size() > x)  
                     result.limbs()[x] = carry;  
               }  
               else  
                  std::copy(pa + i, pa + x, pr + i);  
               result.normalize();  
               result.sign(a.sign());  
            }  
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
static void BM_add_old(benchmark::State& state)  
{  
   int                         bits = state.range(0);  
  
   std::vector<T>& a = get_test_vector_a<T>(bits);  
   std::vector<T>& b = get_test_vector_b<T>(bits);  
   std::vector<T>& c = get_test_vector_c<T>(bits);  
  
   for (auto _ : state)  
   {  
      for (unsigned i = 0; i < a.size(); ++i)  
         add_unsigned_constexpr(c[i].backend(), a[i].backend(), b[i].backend());  
   }  
}  
template <typename T>  
static void BM_add_new(benchmark::State& state)  
{  
   int                         bits = state.range(0);  
  
   std::vector<T>& a = get_test_vector_a<T>(bits);  
   std::vector<T>& b = get_test_vector_b<T>(bits);  
   std::vector<T>& c = get_test_vector_c<T>(bits);  
  
   for (auto _ : state)  
   {  
      for (unsigned i = 0; i < a.size(); ++i)  
         eval_add(c[i].backend(), a[i].backend(), b[i].backend());  
   }  
}  
template <typename T>  
static void BM_add_new_64(benchmark::State& state)  
{  
   int                         bits = state.range(0);  
  
   std::vector<T>& a = get_test_vector_a<T>(bits);  
   std::vector<T>& b = get_test_vector_b<T>(bits);  
   std::vector<T>& c = get_test_vector_c<T>(bits);  
  
   for (auto _ : state)  
   {  
      for (unsigned i = 0; i < a.size(); ++i)  
         add_unsigned_64(c[i].backend(), a[i].backend(), b[i].backend());  
   }  
}  
  
  
BENCHMARK_TEMPLATE(BM_add_old, cpp_int)->RangeMultiplier(2)->Range(1024, 1 << 16);  
BENCHMARK_TEMPLATE(BM_add_new, cpp_int)->RangeMultiplier(2)->Range(1024, 1 << 16);  
BENCHMARK_TEMPLATE(BM_add_new_64, cpp_int)->RangeMultiplier(2)->Range(1024, 1 << 16);  
  
BENCHMARK_MAIN();  
```

---

## Comment 3

> Username: madhur4127  
> Created_at: 2020-05-13 17:52:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-628148932  

I believe my benchmark data also had no carry.  
  
Are there gains with other compilers as well?  
  
What about AMD? ARM? ADC is supported by every architecture so this should be the concern of the compiler to generate instructions. I am concerned with benchmarks though on these platforms as they are quite popular ones.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2020-05-13 19:02:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-628184883  

>I believe my benchmark data also had no carry.  
  
Right, I think I just copied from your benchmark:  
  
```  
      uniform_int_distribution<T> ui(T(1) << (bits - 1) + 1, T(1) << bits);  
```  
  
But `T(1) << (bits - 1) + 1` == `T(1) << bits`, and unusually msvc didn't warn about operator precedence so we both missed it.  So all the test values ended up as powers of 2 :(  
  
>Are there gains with other compilers as well?  
  
Haven't tried yet.  
  
>What about AMD? ARM? ADC is supported by every architecture so this should be the concern of the compiler to generate instructions. I am concerned with benchmarks though on these platforms as they are quite popular ones.  
  
AMD is really a clone of the Intel instruction set?  No idea about ARM, right now I'm only really interested in what we can do for Intel/AMD.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2020-05-13 19:44:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-628206620  

GCC Mingw64 also looks good:  
  
```  
BM_add_old<cpp_int>/1024       83547 ns        83705 ns         8960  
BM_add_old<cpp_int>/2048      150024 ns       150663 ns         4978  
BM_add_old<cpp_int>/4096      267451 ns       266841 ns         2635  
BM_add_old<cpp_int>/8192      511456 ns       515625 ns         1000  
BM_add_old<cpp_int>/16384    1005119 ns      1000977 ns          640  
BM_add_old<cpp_int>/32768    2037512 ns      1992754 ns          345  
BM_add_old<cpp_int>/65536    4075570 ns      4064306 ns          173  
BM_add_new<cpp_int>/1024       57140 ns        57199 ns        11200  
BM_add_new<cpp_int>/2048      105670 ns       104980 ns         6400  
BM_add_new<cpp_int>/4096      168310 ns       168795 ns         4073  
BM_add_new<cpp_int>/8192      327354 ns       329641 ns         2133  
BM_add_new<cpp_int>/16384     652479 ns       645229 ns          896  
BM_add_new<cpp_int>/32768    1519680 ns      1499721 ns          448  
BM_add_new<cpp_int>/65536    2604220 ns      2604167 ns          264  
```

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2020-05-22 11:18:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-632639715  

MSVC faster subtract results:  
  
```  
BM_subtract_old<cpp_int>/1024       41632 ns        41713 ns        17231  
BM_subtract_old<cpp_int>/2048       71214 ns        71498 ns         8960  
BM_subtract_old<cpp_int>/4096      128228 ns       128348 ns         5600  
BM_subtract_old<cpp_int>/8192      242724 ns       239955 ns         2800  
BM_subtract_old<cpp_int>/16384     515532 ns       515625 ns         1000  
BM_subtract_old<cpp_int>/32768    1439661 ns      1464844 ns          448  
BM_subtract_old<cpp_int>/65536    2791531 ns      2761044 ns          249  
BM_subtract_new<cpp_int>/1024       32561 ns        32959 ns        21333  
BM_subtract_new<cpp_int>/2048       53102 ns        53125 ns        10000  
BM_subtract_new<cpp_int>/4096       87317 ns        85794 ns         7467  
BM_subtract_new<cpp_int>/8192      161412 ns       160435 ns         4480  
BM_subtract_new<cpp_int>/16384     375951 ns       376607 ns         1867  
BM_subtract_new<cpp_int>/32768     975524 ns       983099 ns          747  
BM_subtract_new<cpp_int>/65536    1844491 ns      1842752 ns          407  
```

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2020-05-22 12:04:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-632657898  

GCC Minwg results look good too:  
  
```  
BM_subtract_old<cpp_int>/1024       47105 ns        47085 ns        14933  
BM_subtract_old<cpp_int>/2048       86618 ns        87193 ns         8960  
BM_subtract_old<cpp_int>/4096      164086 ns       164958 ns         4073  
BM_subtract_old<cpp_int>/8192      323040 ns       322316 ns         2133  
BM_subtract_old<cpp_int>/16384     674443 ns       662667 ns          896  
BM_subtract_old<cpp_int>/32768    1447775 ns      1443273 ns          498  
BM_subtract_old<cpp_int>/65536    2718159 ns      2698293 ns          249  
BM_subtract_new<cpp_int>/1024       19173 ns        19252 ns        37333  
BM_subtract_new<cpp_int>/2048       32253 ns        32227 ns        21333  
BM_subtract_new<cpp_int>/4096       57915 ns        58594 ns        11200  
BM_subtract_new<cpp_int>/8192      115059 ns       114397 ns         5600  
BM_subtract_new<cpp_int>/16384     296245 ns       291561 ns         2358  
BM_subtract_new<cpp_int>/32768     814646 ns       819615 ns          896  
BM_subtract_new<cpp_int>/65536    1464284 ns      1464844 ns          448  
```  
  
The next problem is that `__has_builtin(__builtin_ia32_addcarryx_u64)` is evaluating to false, even though it is plainly supported on gcc-9 mingw :(

---

## Comment 8

> Username: madhur4127  
> Created_at: 2020-05-22 13:18:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-632686287  

I am also confused about making this intrinsics work. b2 complaints about undefined reference to addcarry_u64.  
  
BTW why are you checking for ADCX?

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2020-05-22 16:35:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-632788933  

>BTW why are you checking for ADCX?  
  
GCC defines these functions like this:  
  
```  
extern __inline unsigned char  
__attribute__((__gnu_inline__, __always_inline__, __artificial__))  
_addcarry_u64 (unsigned char __CF, unsigned long long __X,  
	       unsigned long long __Y, unsigned long long *__P)  
{  
  return __builtin_ia32_addcarryx_u64 (__CF, __X, __Y, __P);  
}  
```  
  
So the intrinsic for `addc` is named `__builtin_ia32_addcarryx_u64` which will suck if in the future they do actually start supporting `adcx`  as they'll have to rename this one :(

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2020-05-22 17:43:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-632825122  

>I am also confused about making this intrinsics work. b2 complaints about undefined reference to addcarry_u64.  
  
What's the actual error?  Which gcc version etc?  Thanks!

---

## Comment 11

> Username: madhur4127  
> Created_at: 2020-05-22 17:55:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-632829592  

> GCC defines these functions like this:  
  
I think you're referring to [adxintrin.h](https://github.com/gcc-mirror/gcc/blob/master/gcc/config/i386/adxintrin.h) which also says:  
  
> Never use <adxintrin.h> directly; include <immintrin.h> instead  
  
I don't know but it feels GCC and LLVM don't want people to use this directly :(  
  
I feel like `__x86_64__` and `i386` architecture check should do the job. Because in GCC these intrinsic are in `i386` and this feature is very much available on almost all processors of this century.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2020-05-22 18:38:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-632845430  

>Never use <adxintrin.h> directly; include <immintrin.h> instead  
  
I am using <immintrin.h>.  
  
>I feel like __x86_64__ and i386 architecture check should do the job. Because in GCC these intrinsic are in i386 and this feature is very much available on almost all processors of this century.  
  
The addc instruction is, but the _addcarry_u64 intrinsic was only introduced by Intel with the ADX stuff, and by gcc for version 9 and later it seems.

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2020-05-27 12:35:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-634630090  

@madhur4127 : Is there anything missing from this that your PR has?  Any reason not to merge?  Thanks!

---

## Review 14 [Commented]

> Username: madhur4127  
> Created_at: 2020-05-27 14:15:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/234#pullrequestreview-419218141  

I am curious about why there aren't any linker errors about `_addcarry_u64` definition (MSVC) as I couldn't see `intrin.h` being included anywhere. [MSVC intrinsics link](https://docs.microsoft.com/en-us/cpp/intrinsics/x64-amd64-intrinsics-list?view=vs-2019).  
  
`__builtin_ia32_addcarryx_u32` works for now but I guess, it is limited to IA32 architecture only (Intel's x86_64), I have to check with AMD CPU though.   
But I am not sure if this is the correct way. It would be optimistic to think it won't break as ADX is not intertwined with ADC.  
  
Thanks for the head-scratching in making this work. I guess this has everything that my PR has and more.

📁 include/boost/multiprecision/cpp_int/add_unsigned.hpp

```diff
  78 |+       result.resize(x + 1, x + 1);
  79 |+       if (result.size() > x)
  80 |+          result.limbs()[x] = static_cast<limb_type>(carry);
```

> Username: madhur4127  
> Created_at: 2020-05-27 13:41:12 UTC  
> Updated_at: 2020-05-27 18:02:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#discussion_r431139519  

I guess `carry` will always be 1. Why not do that instead?

> Username: jzmaddock  
> Created_at: 2020-05-27 16:00:41 UTC  
> Updated_at: 2020-05-27 18:02:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#discussion_r431257662  

Good point, will fix.

---

📁 include/boost/multiprecision/cpp_int/add_unsigned.hpp

```diff
 214 |+       typename CppInt3::const_limb_pointer pb = b.limbs();
 215 |+       typename CppInt1::limb_pointer       pr = result.limbs();
 216 |+       //typename CppInt1::limb_pointer       pr_end = pr + m;
```

> Username: madhur4127  
> Created_at: 2020-05-27 13:51:39 UTC  
> Updated_at: 2020-05-27 18:02:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#discussion_r431147838  

I guess this isn't needed.

> Username: jzmaddock  
> Created_at: 2020-05-27 16:01:03 UTC  
> Updated_at: 2020-05-27 18:02:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#discussion_r431258058  

Nod.

---

📁 include/boost/multiprecision/cpp_int/add_unsigned.hpp

```diff
 251 |+          result.resize(x + 1, x + 1);
 252 |+          if (result.size() > x)
 253 |+             result.limbs()[x] = carry;
```

> Username: madhur4127  
> Created_at: 2020-05-27 13:53:36 UTC  
> Updated_at: 2020-05-27 18:02:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#discussion_r431149337  

same comment. carry is only `1`, I suppose.

> Username: jzmaddock  
> Created_at: 2020-05-27 16:02:15 UTC  
> Updated_at: 2020-05-27 18:02:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#discussion_r431259191  

Will fix.  Good catches, thanks for that!


---

## Comment 15

> Username: jzmaddock  
> Created_at: 2020-05-27 15:59:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-634763013  

Will change immintrin.h to intrin.h on MSVC, though I believe the former is a superset of the latter.

---

## Comment 16

> Username: madhur4127  
> Created_at: 2020-05-28 00:52:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-635025102  

I also believe that intrin.h is subset because Intel's intrinsic page points out to use immintrin.h  
  
But the issue is in the documentation that MSVC because it doesn't reflect that.  
  
_A header file, <intrin.h>, is available that declares prototypes for the common intrinsic functions. Manufacturer-specific intrinsics are available in the <immintrin.h> and <ammintrin.h> header files._ from MSVC

---

## Comment 17

> Username: madhur4127  
> Created_at: 2020-06-01 13:49:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-636871631  

I think this PR fails to compile on GCC.  
  
```  
$ gcc -v  
Target: x86_64-pc-linux-gnu  
gcc version 10.1.0 (GCC)  
  
$ g++ -Wall -Wfatal-errors -Werror -std=c++14 -O3 -I../include bench.cpp -o bench  
In file included from ../include/boost/multiprecision/cpp_int/add_unsigned.hpp:10,  
                 from ../include/boost/multiprecision/cpp_int/add.hpp:12,  
                 from ../include/boost/multiprecision/cpp_int.hpp:2247,  
                 from bench.cpp:3:  
../include/boost/multiprecision/cpp_int/intel_intrinsics.hpp: In function ‘unsigned char boost::multiprecision::detail::subborrow_limb(unsigned char, boost::multiprecision::limb_type, boost::multiprecision::limb_type, boost::multiprecision::limb_type*)’:  
../include/boost/multiprecision/cpp_int/intel_intrinsics.hpp:82:11: error: ‘__builtin_ia32_subborrow_u64’ was not declared in this scope; did you mean ‘__builtin_ia32_sbb_u64’?  
   82 |    return __builtin_ia32_subborrow_u64(carry, a, b, reinterpret_cast<cast_type*>(p_result));  
      |           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
      |           __builtin_ia32_sbb_u64  
```  
  
This is because GCC uses different SBB.  
  
[GCC's adxintrin.h](https://github.com/gcc-mirror/gcc/blob/master/gcc/config/i386/adxintrin.h)  
[LLVM's adxintrin.h](https://github.com/llvm/llvm-project/blob/master/clang/lib/Headers/adxintrin.h)

---

## Comment 18

> Username: madhur4127  
> Created_at: 2020-06-01 13:57:58 UTC  
> Updated_at: 2020-06-01 14:03:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-636875903  

Just checking with ICC  
```  
$ icpc -v                                                           
icpc version 19.1.1.219 (gcc version 10.1.0 compatibility)  
  
$ icpc -Wall -Wfatal-errors -Werror -std=c++14 -O3 -I../include bench.cpp -o bench  
In file included from ../include/boost/multiprecision/cpp_int/add_unsigned.hpp(10),  
                 from ../include/boost/multiprecision/cpp_int/add.hpp(12),  
                 from ../include/boost/multiprecision/cpp_int.hpp(2247),  
                 from bench.cpp(3):  
../include/boost/multiprecision/cpp_int/intel_intrinsics.hpp(68): error: argument of type "cast_type={unsigned long} *" is incompatible with parameter of type "unsigned long long *"  
     return __builtin_ia32_addcarryx_u64(carry, a, b, reinterpret_cast<cast_type*>(p_result));  
```

---

## Comment 19

> Username: jzmaddock  
> Created_at: 2020-06-01 20:20:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-637081108  

>I think this PR fails to compile on GCC.  
  
It's a change in GCC-10 compared to 9.x.  Will fix.

---

## Comment 20

> Username: jzmaddock  
> Created_at: 2020-06-02 18:28:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/234#issuecomment-637728318  

The Intel and gcc-10 issues should be fixed now - thanks for the heads up!  
  
I'm still stuck on the clang issue though as I'm unable to reproduce.

---
