# #218 Faster gcd for double-limb-case. [Merged]

> Username: jzmaddock  
> Created at: 2020-04-14 08:52:23 UTC  
> Updated at: 2020-04-21 21:39:33 UTC  
> Merged at: 2020-04-21 09:59:00 UTC  
> Closed at: 2020-04-21 09:59:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218  

Using the performance test from @madhur4127 I see:  
  
```  
Before:  
BM<cpp_int>/400        8202 ns         7952 ns        74667  
BM<cpp_int>/600       14937 ns        14579 ns        40727  
BM<cpp_int>/800       21660 ns        21449 ns        29867  
BM<cpp_int>/1000      25239 ns        25112 ns        28000  
BM<cpp_int>/1200      34091 ns        34424 ns        21333  
BM<cpp_int>/1400      43222 ns        43493 ns        15448  
BM<cpp_int>/1600      53205 ns        53013 ns        11200  
BM<cpp_int>/1800      62238 ns        62500 ns        10000  
BM<cpp_int>/2000      71333 ns        69754 ns         8960  
  
after:  
BM<cpp_int>/400        2251 ns         2176 ns       373333  
BM<cpp_int>/600        2659 ns         2651 ns       224000  
BM<cpp_int>/800        3826 ns         3770 ns       194783  
BM<cpp_int>/1000       4351 ns         4297 ns       160000  
BM<cpp_int>/1200       5064 ns         4604 ns       112000  
BM<cpp_int>/1400       6112 ns         6138 ns       112000  
BM<cpp_int>/1600       8036 ns         7952 ns       112000  
BM<cpp_int>/1800       9050 ns         8894 ns        89600  
BM<cpp_int>/2000      10376 ns        10463 ns        89600  
```  
  
With msvc.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-04-14 08:56:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-613314647  

With gcc-9 on Ubuntu I see:  
  
```  
Before:  
BM<cpp_int>/400        4096 ns       4096 ns     170201  
BM<cpp_int>/600        6990 ns       6988 ns      97366  
BM<cpp_int>/800       10709 ns      10709 ns      67967  
BM<cpp_int>/1000      14156 ns      14155 ns      49726  
BM<cpp_int>/1200      18216 ns      18214 ns      38690  
BM<cpp_int>/1400      23105 ns      23102 ns      30572  
BM<cpp_int>/1600      29206 ns      29205 ns      24718  
BM<cpp_int>/1800      34244 ns      34242 ns      20436  
BM<cpp_int>/2000      39595 ns      39592 ns      17476  
  
After:  
BM<cpp_int>/400         729 ns        728 ns     932376  
BM<cpp_int>/600         934 ns        934 ns     748705  
BM<cpp_int>/800        1143 ns       1143 ns     610194  
BM<cpp_int>/1000       1405 ns       1390 ns     497985  
BM<cpp_int>/1200       1648 ns       1648 ns     389981  
BM<cpp_int>/1400       1971 ns       1971 ns     357603  
BM<cpp_int>/1600       2270 ns       2270 ns     303139  
BM<cpp_int>/1800       2594 ns       2593 ns     274015  
BM<cpp_int>/2000       2895 ns       2895 ns     241997  
```  
  
Which strangely looks even better than the msvc case, I was expecting this to be the other way around, can anyone please run the test case below with and without this PR?  Thanks!  
  
```  
#include <iostream>  
#include <benchmark/benchmark.h>  
#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/multiprecision/gmp.hpp>  
#include <boost/random.hpp>  
#include <cmath>  
  
using namespace boost::multiprecision;  
using namespace boost::random;  
  
template <typename T>  
static void BM(benchmark::State& state)  
{  
   mt19937_64                  mt;  
   int                         bits = state.range(0);  
   uniform_int_distribution<T> ui((T(1) << (bits - 1) + 1), T(1) << bits);  
   T                           x = ui(mt), z;  
   unsigned __int128           y = 0x12abcdef12abcdef;  
   y <<= 64;  
   y |= 0x5765fde4533;  
   for (auto _ : state)  
   {  
	  ++x;  
	  ++y;  
	  benchmark::DoNotOptimize(z = gcd(x, y));  
   }  
}  
int start = 4e2, end = 2e3, step = 2e2;  
BENCHMARK_TEMPLATE(BM, cpp_int)->DenseRange(start, end, step);  
//BENCHMARK_TEMPLATE(BM, mpz_int)->DenseRange(start, end, step);  
BENCHMARK_MAIN();  
```

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-04-14 09:03:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-613318269  

Weird, Intel is even more of a difference:  
  
```  
Before:  
BM<cpp_int>/400        7827 ns       7814 ns      89159  
BM<cpp_int>/600       13081 ns      13066 ns      53363  
BM<cpp_int>/800       18869 ns      18858 ns      36635  
BM<cpp_int>/1000      24765 ns      24749 ns      27770  
BM<cpp_int>/1200      31790 ns      31790 ns      22109  
BM<cpp_int>/1400      39197 ns      39191 ns      18278  
BM<cpp_int>/1600      46418 ns      46412 ns      14279  
BM<cpp_int>/1800      53226 ns      53226 ns      12200  
BM<cpp_int>/2000      60423 ns      60317 ns      11374  
  
after:  
BM<cpp_int>/400         743 ns        743 ns     906386  
BM<cpp_int>/600         931 ns        931 ns     737220  
BM<cpp_int>/800        1120 ns       1120 ns     613565  
BM<cpp_int>/1000       1401 ns       1384 ns     516753  
BM<cpp_int>/1200       1704 ns       1681 ns     415419  
BM<cpp_int>/1400       2053 ns       2048 ns     338719  
BM<cpp_int>/1600       2704 ns       2700 ns     287685  
BM<cpp_int>/1800       2671 ns       2671 ns     245763  
BM<cpp_int>/2000       3089 ns       3067 ns     218096  
```  
  
Which is a 20x performance improvement in the best case.... I suspect I'm missing something.

---

## Comment 3

> Username: madhur4127  
> Created_at: 2020-04-14 11:54:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-613398289  

before: current develop  
after: this branch   
  
```  
// gcc version 9.3.0 (Arch Linux 9.3.0-1) with -O2  
  
// before:  
Benchmark                 Time             CPU   Iterations  
-----------------------------------------------------------  
BM<cpp_int>/400        8514 ns         8389 ns        84870  
BM<cpp_int>/600       14329 ns        14146 ns        48718  
BM<cpp_int>/800       21341 ns        21021 ns        33786  
BM<cpp_int>/1000      29499 ns        29078 ns        24297  
BM<cpp_int>/1200      37670 ns        37153 ns        18513  
BM<cpp_int>/1400      48980 ns        47484 ns        14916  
BM<cpp_int>/1600      58085 ns        57331 ns        11721  
BM<cpp_int>/1800      71424 ns        70409 ns         9726  
BM<cpp_int>/2000      86670 ns        85376 ns         8525  
  
// after:  
BM<cpp_int>/400        1332 ns         1319 ns       476268  
BM<cpp_int>/600        1759 ns         1743 ns       406714  
BM<cpp_int>/800        2219 ns         2201 ns       320167  
BM<cpp_int>/1000       2733 ns         2702 ns       258713  
BM<cpp_int>/1200       3571 ns         3514 ns       201295  
BM<cpp_int>/1400       4096 ns         4052 ns       158772  
BM<cpp_int>/1600       4812 ns         4770 ns       142680  
BM<cpp_int>/1800       5301 ns         5257 ns       132721  
BM<cpp_int>/2000       6126 ns         6064 ns       118732  
```  
and with clang  
```  
// clang version 9.0.1  
//before:  
Benchmark                 Time             CPU   Iterations  
-----------------------------------------------------------  
BM<cpp_int>/400       13124 ns        13011 ns        50181  
BM<cpp_int>/600       22576 ns        22390 ns        31401  
BM<cpp_int>/800       33485 ns        33184 ns        21629  
BM<cpp_int>/1000      44198 ns        43873 ns        15942  
BM<cpp_int>/1200      56576 ns        56140 ns        12287  
BM<cpp_int>/1400      69098 ns        68536 ns         9849  
BM<cpp_int>/1600      84288 ns        83680 ns         8420  
BM<cpp_int>/1800      99183 ns        98370 ns         6945  
BM<cpp_int>/2000     117451 ns       116302 ns         6174  
  
// after:  
Benchmark                 Time             CPU   Iterations  
-----------------------------------------------------------  
BM<cpp_int>/400        1427 ns         1351 ns       513522  
BM<cpp_int>/600        1844 ns         1809 ns       389109  
BM<cpp_int>/800        2324 ns         2285 ns       308563  
BM<cpp_int>/1000       2844 ns         2760 ns       254928  
BM<cpp_int>/1200       3406 ns         3325 ns       199217  
BM<cpp_int>/1400       3868 ns         3812 ns       184087  
BM<cpp_int>/1600       4662 ns         4564 ns       136071  
BM<cpp_int>/1800       5212 ns         5129 ns       100000  
BM<cpp_int>/2000       5659 ns         5588 ns       125678  
```

---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-04-14 11:59:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-613400263  

@madhur4127 : Could you run it the benchmark under `perf` and see what instructions are hot?

---

## Comment 5

> Username: madhur4127  
> Created_at: 2020-04-14 12:30:24 UTC  
> Updated_at: 2020-04-14 12:33:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-613414465  

@jzmaddock can you post benchmarks wrt clang? I am seeing a strange slowdown here.  
  
@NAThompson, does this help (clang generated binary)?  
Right shift generic: 37% (bitwise.hpp:578)  
Subtract unsigned: 21%   
eval_gcd: 18%  
eval_lsb: 11%  
eval_right_shift: 11%  
  
![image](https://user-images.githubusercontent.com/29328609/79224991-ad307c80-7e79-11ea-8ef1-9f784d8e5cd2.png)

---

## Comment 6

> Username: NAThompson  
> Created_at: 2020-04-14 12:55:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-613425887  

@madhur4127 : This is a nice callstack, and useful information. But the asm is displayed via  
  
```  
$ perf record -g ./bench.x  
$ perf report -g -M intel  
```  
  
and then hitting the `a` key on the function of interest.

---

## Comment 7

> Username: madhur4127  
> Created_at: 2020-04-14 13:25:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-613441069  

```  
right_shift_generic: 38.5%  
       │     for (; i + offset + 1 < ors; ++i)                                                                                    ▒  
 11.33 │       mov   eax,esi  ****                                                                                                    ▒  
  2.73 │       lea   rcx,[rax+rdx*1]                                                                                              ◆  
  0.12 │       lea   r13,[rax+rdx*1]                                                                                              ▒  
  1.21 │       add   r13,0x1                                                                                                      ▒  
  8.77 │       mov   rdi,QWORD PTR [r15+rcx*8]                                                                                    ▒  
  3.37 │       mov   ecx,r11d                                                                                                     ▒  
  0.14 │       shr   rdi,cl                                                                                                       ▒  
 14.42 │       add   esi,0x1****                                                                                         ▒  
  0.06 │       lea   rbx,[r15+rax*8]                                                                                              ▒  
  1.29 │       mov   QWORD PTR [r15+rax*8],rdi                                                                                    ▒  
 10.21 │       cmp   r13,r10*****  
  
       │     {                                                                                                                    ◆  
       │     pr[i] = pr[i + offset] >> shift;                                                                                     ▒  
       │     pr[i] |= pr[i + offset + 1] << (Int::limb_bits - shift);                                                             ▒  
  3.31 │       mov   rax,QWORD PTR [r15+r13*8]                                                                                    ▒  
  0.25 │       mov   ecx,r14d                                                                                                     ▒  
  1.48 │       shl   rax,cl                                                                                                       ▒  
 12.73 │       or    rax,rdi****  
```  
```  
subtract_unsigned: 20.47%  
       │     BOOST_MP_FORCEINLINE unsigned              capacity() const BOOST_NOEXCEPT { return m_internal ? internal_limb_count :  
  0.15 │       cmp    BYTE PTR [rcx+0x15],0x0  
  5.24 │     → je****     8f64 <void boost::multiprecision::backends::subtract_unsigned<boost::multiprecision::backends::cpp_int_backe  
```

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2020-04-14 14:06:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-613463608  

@madhur4127 : I see about what you see with both clang and Intel: they are much slower with develop code, and about the same as gcc or maybe a *little* slower after this PR.  Here's clang:  
  
```  
develop:  
BM<cpp_int>/400       15189 ns      14913 ns      42191  
BM<cpp_int>/600       25509 ns      25507 ns      27530  
BM<cpp_int>/800       38661 ns      38659 ns      19448  
BM<cpp_int>/1000      47334 ns      47285 ns      14529  
BM<cpp_int>/1200      62525 ns      62522 ns      11553  
BM<cpp_int>/1400      76307 ns      76294 ns       9176  
BM<cpp_int>/1600      90474 ns      90469 ns       7359  
BM<cpp_int>/1800     105460 ns     105460 ns       6655  
BM<cpp_int>/2000     122146 ns     122115 ns       5691  
  
With PR:  
BM<cpp_int>/400        1668 ns       1645 ns     368635  
BM<cpp_int>/600        2081 ns       2078 ns     329984  
BM<cpp_int>/800        2585 ns       2574 ns     265050  
BM<cpp_int>/1000       3026 ns       3025 ns     231518  
BM<cpp_int>/1200       4120 ns       4119 ns     184872  
BM<cpp_int>/1400       4046 ns       4043 ns     173423  
BM<cpp_int>/1600       5075 ns       5074 ns     139569  
BM<cpp_int>/1800       5432 ns       5430 ns     119946  
BM<cpp_int>/2000       5863 ns       5862 ns     117578  
```

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2020-04-14 14:08:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-613465199  

>Right shift generic: 37% (bitwise.hpp:578)  
Subtract unsigned: 21%  
eval_gcd: 18%  
eval_lsb: 11%  
eval_right_shift: 11%  
  
That's sort of what I would expect - it's the binary gcd algorithm so right shifting is what it's all about!  
  
I'm a bit surprised that eval_lsb is showing up in there: that should reduce to an inline intrinsic (single instruction), does it show up in the gcc profile?

---

## Comment 10

> Username: madhur4127  
> Created_at: 2020-04-14 15:12:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-613501386  

@jzmaddock, Can you confirm whether the call `res = eval_gcd(res, b);` calls `limb_type eval_gcd(limb_type u, limb_type v)` when both `res` and `b` are double_limb_type?

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2020-04-14 16:03:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-613530915  

> Can you confirm whether the call res = eval_gcd(res, b); calls limb_type eval_gcd(limb_type u, limb_type v) when both res and b are double_limb_type?  
  
Oh blast, that's the bug I was missing - good catch!  
  
Correcting that and the speedup almost disappears - there's still a little I think, but I'm running on battery at present so my benchmarks are screwy... more later, in the mean time fix pushed.  
  
BTW this indicates a lack of testing somewhere.... need to investigate that.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2020-04-14 16:08:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-613533952  

That fix has issues in anything except -std=gnu++17... :(

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2020-04-14 17:27:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-613575230  

>This will generate (128+64) bit result which will overflow res.  
  
For some reason github isn't showing me where that comment is located... strange... in any case the code in that situation is identical to taking the modulus with a limb_type.  
  
Both `res` and `two_n_mod` fit in a limb_type so as before multiply and add of 3 64-bit values must always fit in a 128 bit integer.  
  
But quite likely I may be missing the point ;)

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2020-04-15 14:17:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-614067433  

The tests are now extended and catch the eval_gcd overload issue you spotted plus a couple of other minor issues.  With a couple of fixes everything is now pasasing for me locally.  
  
Code shows esentially no change for msvc:  
  
  
  | before | after  
-- | -- | --  
BM_dl<cpp_int>/400 | 5607 | 6973  
BM_dl<cpp_int>/600 | 9281 | 12062  
BM_dl<cpp_int>/800 | 14870 | 15421  
BM_dl<cpp_int>/1000 | 22388 | 21411  
BM_dl<cpp_int>/1200 | 27335 | 28841  
BM_dl<cpp_int>/1400 | 37721 | 35733  
BM_dl<cpp_int>/1600 | 43462 | 42252  
BM_dl<cpp_int>/1800 | 53100 | 54823  
BM_dl<cpp_int>/2000 | 73554 | 65557

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2020-04-15 15:24:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-614106274  

GCC/ Ubuntu though shows a massive improvement:  
  
  
  | Before | After  
-- | -- | --  
BM<cpp_int>/400 | 34156 | 3065  
BM<cpp_int>/600 | 58822 | 4090  
BM<cpp_int>/800 | 84239 | 4943  
BM<cpp_int>/1000 | 108124 | 5751  
BM<cpp_int>/1200 | 134725 | 6734  
BM<cpp_int>/1400 | 164721 | 7800  
BM<cpp_int>/1600 | 197906 | 9453  
BM<cpp_int>/1800 | 229427 | 10814  
BM<cpp_int>/2000 | 267514 | 11935

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2020-04-15 15:46:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-614118904  

My bad, my test case for msvc was using a signed __int64 and I hadn't hooked signed types up to the new code, after that the improvement is there with msvc as well:  
  
  
  | before | after  
-- | -- | --  
BM_dl<cpp_int>/400 | 6974 | 2559  
BM_dl<cpp_int>/600 | 12196 | 3264  
BM_dl<cpp_int>/800 | 18668 | 3367  
BM_dl<cpp_int>/1000 | 30121 | 4159  
BM_dl<cpp_int>/1200 | 35904 | 5169  
BM_dl<cpp_int>/1400 | 45851 | 6517  
BM_dl<cpp_int>/1600 | 62737 | 8581  
BM_dl<cpp_int>/1800 | 71983 | 9857  
BM_dl<cpp_int>/2000 | 82680 | 11014

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2020-04-15 18:27:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-614203975  

Also ran a very quick test of an "adaptive" strategy that uses a Euclid step (calls eval_modulus) whenever the two values differ in size by 2 limbs or more:  
  
  
Test (input limb sizes) | Develop | adaptive with modulus | GMP  
-- | -- | -- | --  
BM<cpp_int>/4/3 | 2196 | 1986 | 743  
BM<cpp_int>/6/3 | 3672 | 2278 | 666  
BM<cpp_int>/6/5 | 4216 | 4096 | 993  
BM<cpp_int>/8/3 | 4509 | 1876 | 712  
BM<cpp_int>/8/5 | 5335 | 3843 | 1154  
BM<cpp_int>/8/7 | 6214 | 6386 | 1467  
BM<cpp_int>/10/3 | 6494 | 2370 | 715  
BM<cpp_int>/10/5 | 6267 | 4382 | 1286  
BM<cpp_int>/10/7 | 6508 | 6096 | 1606  
BM<cpp_int>/10/9 | 6942 | 10126 | 1805  
BM<cpp_int>/12/3 | 5430 | 2768 | 740  
BM<cpp_int>/12/5 | 6808 | 4288 | 1348  
BM<cpp_int>/12/7 | 7508 | 6038 | 1804  
BM<cpp_int>/12/9 | 8282 | 7300 | 2127  
BM<cpp_int>/12/11 | 9228 | 9918 | 2508  
BM<cpp_int>/14/3 | 7197 | 2071 | 761  
BM<cpp_int>/14/5 | 7927 | 3910 | 1378  
BM<cpp_int>/14/7 | 8704 | 5653 | 1972  
BM<cpp_int>/14/9 | 10041 | 7368 | 2228  
BM<cpp_int>/14/11 | 10725 | 9522 | 2465  
BM<cpp_int>/14/13 | 11646 | 12318 | 2694  
BM<cpp_int>/16/3 | 8332 | 2312 | 790  
BM<cpp_int>/16/5 | 9793 | 3960 | 1383  
BM<cpp_int>/16/7 | 12129 | 5548 | 2007  
BM<cpp_int>/16/9 | 11595 | 7259 | 2439  
BM<cpp_int>/16/11 | 12465 | 9361 | 2753  
BM<cpp_int>/16/13 | 13386 | 11684 | 3096  
BM<cpp_int>/16/15 | 14278 | 14786 | 3503  
BM<cpp_int>/18/3 | 9716 | 2520 | 780  
BM<cpp_int>/18/5 | 11173 | 3881 | 1417  
BM<cpp_int>/18/7 | 11569 | 5764 | 1969  
BM<cpp_int>/18/9 | 13139 | 7432 | 2571  
BM<cpp_int>/18/11 | 14034 | 9357 | 2911  
BM<cpp_int>/18/13 | 15041 | 11904 | 3249  
BM<cpp_int>/18/15 | 16215 | 14401 | 3585  
BM<cpp_int>/18/17 | 17357 | 17602 | 3945  
BM<cpp_int>/20/3 | 11620 | 3021 | 822  
BM<cpp_int>/20/5 | 12040 | 4281 | 1416  
BM<cpp_int>/20/7 | 13797 | 5936 | 2008  
BM<cpp_int>/20/9 | 14715 | 7677 | 2602  
BM<cpp_int>/20/11 | 15816 | 9548 | 3114  
BM<cpp_int>/20/13 | 17047 | 12172 | 3457  
BM<cpp_int>/20/15 | 18053 | 14549 | 3962  
BM<cpp_int>/20/17 | 19206 | 16983 | 4348  
BM<cpp_int>/20/19 | 20515 | 20946 | 4920  
BM<cpp_int>/22/3 | 12414 | 2894 | 820  
BM<cpp_int>/22/5 | 14458 | 4012 | 1473  
BM<cpp_int>/22/7 | 15435 | 6123 | 2041  
BM<cpp_int>/22/9 | 16511 | 7738 | 2612  
BM<cpp_int>/22/11 | 17622 | 9990 | 3198  
BM<cpp_int>/22/13 | 18898 | 12186 | 3628  
BM<cpp_int>/22/15 | 20434 | 14794 | 4036  
BM<cpp_int>/22/17 | 21209 | 17310 | 4436  
BM<cpp_int>/22/19 | 22038 | 20061 | 4950  
BM<cpp_int>/22/21 | 23215 | 24055 | 5532  
  
Which looks promising, though nothing like GMP's performance, should really be compared to Lehmers algorithm I guess (which uses the same stragety as part of a modified Euclid GCD).

---

## Comment 18

> Username: madhur4127  
> Created_at: 2020-04-16 06:58:14 UTC  
> Updated_at: 2020-04-16 07:02:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-614452518  

> Also ran a very quick test of an "adaptive" strategy that uses a Euclid step (calls eval_modulus) whenever the two values differ in size by 2 limbs or more  
  
> BM<cpp_int>/22/21 | 23215 | 24055  
  
I think a crossover point would have been better in this case, because the division is way too slow as a first step. For bigger limbs, I think it might dominate Binary GCD as a whole.  
  
Can you share what these numbers are (out of 3 columns in benchmark)? iterations? CPU time? Overall time?

---

## Comment 19

> Username: jzmaddock  
> Created_at: 2020-04-16 08:06:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-614485368  

>> BM<cpp_int>/22/21 | 23215 | 24055  
  
>I think a crossover point would have been better in this case, because the division is way too slow as a first step. For bigger limbs, I think it might dominate Binary GCD as a whole.  
  
Nod.  I'm still playing with the heuristics, using a difference of more than 2 limbs in size seems to work slightly better.  There's quite a bit of difference in the timings between runs, so you have to take the figures with some caution.  
  
>Can you share what these numbers are (out of 3 columns in benchmark)? iterations? CPU time? Overall time?  
  
Sorry, overall time.  
  
I need to test with much larger limb counts as well.

---

## Comment 20

> Username: NAThompson  
> Created_at: 2020-04-16 09:16:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-614523694  

> There's quite a bit of difference in the timings between runs, so you have to take the figures with some caution.  
  
Try the option `--benchmark_min_time=5` if you can't get reliable numbers. Also, `sudo cpupower frequency-set --governor performance` on Linux.

---

## Comment 21

> Username: jzmaddock  
> Created_at: 2020-04-16 15:46:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-614733919  

Thanks Nick, just needed to leave it running on an otherwise idle machine for a bit.  
  
So.... some more numbers are in, and as @madhur4127 suspected for larger limb counts the modulus gets expensive slightly more rapidly than the binary gcd does, though there's not as much in it as you might think.  Sorry for the big table, but here's the numbers using a straight "3 limbs or more => Euclid step" heuristic;  
  
  
Limb counts  | Develop/cpu time | New/cpu time | Comments  
-- | -- | -- | --  
BM_old<cpp_int>/20/20 | 20560 | 20332 |    
BM_old<cpp_int>/20/17 | 19019 | 16479 |    
BM_old<cpp_int>/20/10 | 15067 | 8287 |    
BM_old<cpp_int>/70/70 | 157529 | 156366 |    
BM_old<cpp_int>/70/67 | 151528 | 146983 |    
BM_old<cpp_int>/70/60 | 148294 | 122871 |    
BM_old<cpp_int>/70/50 | 132909 | 91914 |    
BM_old<cpp_int>/70/30 | 108643 | 44759 |    
BM_old<cpp_int>/120/120 | 409593 | 408082 |    
BM_old<cpp_int>/120/117 | 405059 | 393822 | 3 limb heuristic still good to here  
BM_old<cpp_int>/120/110 | 392369 | 351684 |    
BM_old<cpp_int>/120/100 | 375279 | 300000 |    
BM_old<cpp_int>/120/80 | 335693 | 204787 |    
BM_old<cpp_int>/120/40 | 259304 | 82520 |    
BM_old<cpp_int>/170/170 | 787644 | 819615 | Not so good here...  
BM_old<cpp_int>/170/167 | 778925 | 816369 |    
BM_old<cpp_int>/170/160 | 760458 | 758929 | But 10 limbs onward looks like a gain....  
BM_old<cpp_int>/170/150 | 732208 | 659887 |    
BM_old<cpp_int>/170/130 | 682760 | 512201 |    
BM_old<cpp_int>/170/90 | 575449 | 266397 |    
BM_old<cpp_int>/220/220 | 1283189 | 1289063 |    
BM_old<cpp_int>/220/217 | 1275798 | 1283189 |    
BM_old<cpp_int>/220/210 | 1250930 | 1246917 |    
BM_old<cpp_int>/220/200 | 1228780 | 1110840 |    
BM_old<cpp_int>/220/180 | 1170154 | 941685 |    
BM_old<cpp_int>/220/140 | 1016475 | 609627 |    
BM_old<cpp_int>/270/270 | 1885412 | 1930778 |    
BM_old<cpp_int>/270/267 | 1883371 | 1858261 |    
BM_old<cpp_int>/270/260 | 1845094 | 1785141 |    
BM_old<cpp_int>/270/250 | 1818571 | 1669706 |    
BM_old<cpp_int>/270/230 | 1735394 | 1428148 |    
BM_old<cpp_int>/270/190 | 1571567 | 1024276 |    
BM_old<cpp_int>/320/320 | 2600932 | 2639752 |    
BM_old<cpp_int>/320/317 | 2615426 | 2583511 |    
BM_old<cpp_int>/320/310 | 2589779 | 2461787 |    
BM_old<cpp_int>/320/300 | 2545378 | 2329892 |    
BM_old<cpp_int>/320/280 | 2430354 | 2118644 |    
BM_old<cpp_int>/320/240 | 2251272 | 1843262 |    
BM_old<cpp_int>/370/370 | 3475192 | 4282756 | 3 limb heuristic looking worse here  
BM_old<cpp_int>/370/367 | 3435094 | 3890562 |    
BM_old<cpp_int>/370/360 | 3452332 | 3924419 |    
BM_old<cpp_int>/370/350 | 3354897 | 3669085 |    
BM_old<cpp_int>/370/330 | 3324963 | 3290086 | Need 40 limb difference for a gain?  
BM_old<cpp_int>/370/290 | 3068740 | 2740022 |    
BM_old<cpp_int>/420/420 | 4466694 | 5195552 |    
BM_old<cpp_int>/420/417 | 4443359 | 5099693 |    
BM_old<cpp_int>/420/410 | 4443359 | 4947917 |    
BM_old<cpp_int>/420/400 | 4296875 | 4760742 |    
BM_old<cpp_int>/420/380 | 4172942 | 4345507 |    
BM_old<cpp_int>/420/340 | 3928917 | 3515291 |    
BM_old<cpp_int>/470/470 | 5522088 | 6573933 |    
BM_old<cpp_int>/470/467 | 5452474 | 6469727 |    
BM_old<cpp_int>/470/460 | 5411784 | 6301153 |    
BM_old<cpp_int>/470/450 | 5333439 | 6032293 |    
BM_old<cpp_int>/470/430 | 5214724 | 4685408 |    
BM_old<cpp_int>/470/390 | 4984663 | 3895349 |    
BM_old<cpp_int>/520/520 | 6700000 | 6713867 |    
BM_old<cpp_int>/520/517 | 6725000 | 6616211 |    
BM_old<cpp_int>/520/510 | 6625000 | 6445313 |    
BM_old<cpp_int>/520/500 | 6567383 | 6213716 |    
BM_old<cpp_int>/520/480 | 6407203 | 5767906 |    
BM_old<cpp_int>/520/440 | 6123004 | 4901961 |    
BM_old<cpp_int>/570/570 | 7975569 | 8005218 |    
BM_old<cpp_int>/570/567 | 8005218 | 7886622 |    
BM_old<cpp_int>/570/560 | 7945920 | 7741348 |    
BM_old<cpp_int>/570/550 | 7812500 | 7449777 |    
BM_old<cpp_int>/570/530 | 7645089 | 6981383 |    
BM_old<cpp_int>/570/490 | 7338170 | 6033416 |    
BM_old<cpp_int>/620/620 | 9381975 | 9460034 |    
BM_old<cpp_int>/620/617 | 9396601 | 9388706 |    
BM_old<cpp_int>/620/610 | 9285910 | 9148849 |    
BM_old<cpp_int>/620/600 | 9217379 | 8838718 |    
BM_old<cpp_int>/620/580 | 9044276 | 8351824 |    
BM_old<cpp_int>/620/540 | 8723958 | 7277397 |    
BM_old<cpp_int>/670/670 | 10904948 | 10945638 |    
BM_old<cpp_int>/670/667 | 10897436 | 10857372 |    
BM_old<cpp_int>/670/660 | 10937500 | 10601266 |    
BM_old<cpp_int>/670/650 | 10719937 | 10365479 |    
BM_old<cpp_int>/670/630 | 10561709 | 9828629 |    
BM_old<cpp_int>/670/590 | 10190217 | 8649554 |

---

## Comment 22

> Username: jzmaddock  
> Created_at: 2020-04-16 15:47:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-614734811  

Mostly for posterity.... here's the current test code, new and old are compared in the executable:  
  
```  
#include <iostream>  
#include <benchmark/benchmark.h>  
#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/multiprecision/gmp.hpp>  
#include <boost/random.hpp>  
#include <cmath>  
  
using namespace boost::multiprecision;  
using namespace boost::random;  
  
namespace boost {  
   namespace multiprecision {  
      namespace backends {  
  
         template <unsigned MinBits1, unsigned MaxBits1, cpp_integer_type SignType1, cpp_int_check_type Checked1, class Allocator1>  
         inline BOOST_MP_CXX14_CONSTEXPR typename enable_if_c<!is_trivial_cpp_int<cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1> >::value>::type  
            eval_gcd_new(  
               cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1>& result,  
               const cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1>& a,  
               const cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1>& b)  
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
  
            cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1> u(a), v(b);  
  
            int s = eval_get_sign(u);  
  
            /* GCD(0,x) := x */  
            if (s < 0)  
            {  
               u.negate();  
            }  
            else if (s == 0)  
            {  
               result = v;  
               return;  
            }  
            s = eval_get_sign(v);  
            if (s < 0)  
            {  
               v.negate();  
            }  
            else if (s == 0)  
            {  
               result = u;  
               return;  
            }  
  
            /* Let shift := lg K, where K is the greatest power of 2  
            dividing both u and v. */  
  
            unsigned us = eval_lsb(u);  
            unsigned vs = eval_lsb(v);  
            int      shift = (std::min)(us, vs);  
            eval_right_shift(u, us);  
            eval_right_shift(v, vs);  
  
            do  
            {  
               /* Now u and v are both odd, so diff(u, v) is even.  
               Let u = min(u, v), v = diff(u, v)/2. */  
               s = u.compare(v);  
               if (s > 0)  
                  u.swap(v);  
               if ((u.size() + 2 < v.size()) || ((u.size() <= 2) && (v.size() > 4)))  
               {  
                  eval_modulus(v, v, u);  
                  u.swap(v);  
                  continue;  
               }  
               if (s == 0)  
                  break;  
               if (v.size() <= 2)  
               {  
                  if (v.size() == 1)  
                     u = eval_gcd(*v.limbs(), *u.limbs());  
                  else  
                  {  
                     double_limb_type i = v.limbs()[0] | (static_cast<double_limb_type>(v.limbs()[1]) << sizeof(limb_type) * CHAR_BIT);  
                     double_limb_type j = (u.size() == 1) ? *u.limbs() : u.limbs()[0] | (static_cast<double_limb_type>(u.limbs()[1]) << sizeof(limb_type) * CHAR_BIT);  
                     u = eval_gcd(i, j);  
                  }  
                  break;  
               }  
               eval_subtract(v, u);  
               vs = eval_lsb(v);  
               eval_right_shift(v, vs);  
            } while (true);  
  
            result = u;  
            eval_left_shift(result, shift);  
         }  
  
      }  
   }  
   }  
  
/*  
template <typename T>  
static void BM_dl(benchmark::State& state)  
{  
   mt19937_64                  mt;  
   int                         bits = state.range(0);  
   uniform_int_distribution<T> ui((T(1) << (bits - 1) + 1), T(1) << bits);  
   T                           x = ui(mt), z;  
   std::int64_t               y = 0x12abcdef12abcdef;  
   y <<= 64;  
   y |= 0x5765fde4533;  
   for (auto _ : state)  
   {  
      ++x;  
      ++y;  
      benchmark::DoNotOptimize(z = gcd(x, y));  
   }  
}  
int start = 4e2, end = 2e3, step = 2e2;  
BENCHMARK_TEMPLATE(BM_dl, cpp_int)->DenseRange(start, end, step);   
*/  
  
inline int test_proc(cpp_int&z, cpp_int const& x, cpp_int const& y)  
{  
   ::boost::multiprecision::backends::eval_gcd_new(z.backend(), x.backend(), y.backend());  
   return z.backend().size();  
}  
inline int test_proc_old(cpp_int&z, cpp_int const& x, cpp_int const& y)  
{  
   ::boost::multiprecision::backends::eval_gcd(z.backend(), x.backend(), y.backend());  
   return z.backend().size();  
}  
  
  
template <typename T>  
static void BM_new(benchmark::State& state)  
{  
   mt19937_64                  mt;  
   int                         a_limbs = state.range(0);  
   int                         b_limbs = state.range(1);  
   T min_a = T(1) << ((a_limbs - 1) * sizeof(boost::multiprecision::limb_type) * CHAR_BIT);  
   T max_a = T(1) << (a_limbs * sizeof(boost::multiprecision::limb_type) * CHAR_BIT);  
   T min_b = T(1) << ((b_limbs - 1) * sizeof(boost::multiprecision::limb_type) * CHAR_BIT);  
   T max_b = T(1) << (b_limbs * sizeof(boost::multiprecision::limb_type) * CHAR_BIT);  
   uniform_int_distribution<T> a_dist(min_a, max_a);  
   uniform_int_distribution<T> b_dist(min_b, max_b);  
   T                           x = a_dist(mt), z;  
   T                           y = b_dist(mt);  
  
   for (auto _ : state)  
   {  
      ++x;  
      ++y;  
      benchmark::DoNotOptimize(test_proc(z, x, y));  
   }  
}  
  
template <typename T>  
static void BM_old(benchmark::State& state)  
{  
   mt19937_64                  mt;  
   int                         a_limbs = state.range(0);  
   int                         b_limbs = state.range(1);  
   T min_a = T(1) << ((a_limbs - 1) * sizeof(boost::multiprecision::limb_type) * CHAR_BIT);  
   T max_a = T(1) << (a_limbs * sizeof(boost::multiprecision::limb_type) * CHAR_BIT);  
   T min_b = T(1) << ((b_limbs - 1) * sizeof(boost::multiprecision::limb_type) * CHAR_BIT);  
   T max_b = T(1) << (b_limbs * sizeof(boost::multiprecision::limb_type) * CHAR_BIT);  
   uniform_int_distribution<T> a_dist(min_a, max_a);  
   uniform_int_distribution<T> b_dist(min_b, max_b);  
   T                           x = a_dist(mt), z;  
   T                           y = b_dist(mt);  
  
   for (auto _ : state)  
   {  
      ++x;  
      ++y;  
      benchmark::DoNotOptimize(test_proc_old(z, x, y));  
   }  
}  
  
static void CustomArguments(benchmark::internal::Benchmark* b) {  
  
   static const int subs[] = { 0, 3, 10, 20, 40, 80 };  
  
   for (int i = 20; i <= 700; i += 50)  
   {  
      for (int j = 0; j < 6; ++j)  
      {  
         if (subs[j] < i)  
            b->Args({ i, i - subs[j] });  
      }  
   }  
}  
  
BENCHMARK_TEMPLATE(BM_old, cpp_int)->Apply(CustomArguments);  
BENCHMARK_TEMPLATE(BM_new, cpp_int)->Apply(CustomArguments);  
//BENCHMARK_TEMPLATE(BM, mpz_int)->Apply(CustomArguments);  
  
BENCHMARK_MAIN();  
```

---

## Comment 23

> Username: madhur4127  
> Created_at: 2020-04-16 16:25:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-614756313  

> Sorry for the big table,  
  
Thanks for the table. Appreciate the time.  
  
> BM_old<cpp_int>/470/450 | 5333439 | 6032293 |  
  
Anomaly.  
  
> BM_old<cpp_int>/470/430 | 5214724 | 4685408 |  
> BM_old<cpp_int>/670/650 | 10719937 | 10365479 |  
  
Even 20 limb difference isn't enough. I guess we could try something like `b.size() >= 1.1 * a.size()`.   
  
I think this will require some extensive benchmarking as the behavior of the algorithm cannot be judged even with this data. @jzmaddock, I think there is a need for benchmark keeping in mind that division must be run on its max case (or worst input) and/or with GCD's worst case. I don't know whether the current input is average input or good input for division (sorry :( ).  
  
Excitingly, I think there is a need for a new algorithm here. Either the GCD's algorithm needs to change or division.

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2020-04-16 18:32:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-614824086  

> Even 20 limb difference isn't enough. I guess we could try something like b.size() >= 1.1 * a.size().  
  
Using "5% difference in size and at least 3 limbs" seems to work as well as anything.  
  
>I think this will require some extensive benchmarking as the behavior of the algorithm cannot be judged even with this data. @jzmaddock, I think there is a need for benchmark keeping in mind that division must be run on its max case (or worst input) and/or with GCD's worst case. I don't know whether the current input is average input or good input for division (sorry :( ).  
  
It's difficult to devise "typical" data to test with - plus for large limb counts getting reliable data takes a lot of runtime :(  
  
One sanity check at least, would be to benchmark some rational-number calculations - any suggestions?

---

## Comment 25

> Username: jzmaddock  
> Created_at: 2020-04-17 08:15:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-615110725  

You know after sleeping on this, I can't help but wonder if this is optimising the wrong thing: as noted by Knuth, v/u is almost always 1,2 or 3 and large differences are rare.  Likewise in the binary case, we almost always right shift by 1 bit.  That said, as long as this doesn't handicap the general case, it is a big win for greatly mismatched arguments.

---

## Comment 26

> Username: jzmaddock  
> Created_at: 2020-04-21 09:58:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-617080562  

Merging for now as it's an easy addition that helps a little.  
  
GMP does this also, but uses Lehmers algorithm for larger limb cases - so yes @madhur4127 we do need a new algorithm.

---

## Review 27 [Commented]

> Username: NAThompson  
> Created_at: 2020-04-21 10:05:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/218#pullrequestreview-395904539  

📁 include/boost/multiprecision/cpp_int/misc.hpp

```diff
 552 |+       {
 553 |+          //
 554 |+          // Speical case: if u and v differ considerably in size, then a Euclid step
```

> Username: NAThompson  
> Created_at: 2020-04-18 11:11:08 UTC  
> Updated_at: 2020-04-21 10:05:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#discussion_r410684072  

Typo: Speical->Special


---

## Comment 28

> Username: madhur4127  
> Created_at: 2020-04-21 14:06:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-617202555  

@jzmaddock, I was thinking of working in making squaring faster. Ideally in naive base case multiplication and karatsuba too, things can be optimized to potentially half the existing runtime if redundant computation are eliminated.  
  
I was thinking of first optimizing comba, then karatsuba and in the process of this, I also want to reduce the temporaries created.  
  
These should be small changes but having huge impact since we already have the base algorithm ready.  
  
The reason I want to switch to optimize multiplication because GCD, Division can be reduced to multiplication with a log factor. That way we can make performance gains on multiple frontiers.

---

## Comment 29

> Username: madhur4127  
> Created_at: 2020-04-21 14:11:43 UTC  
> Updated_at: 2020-04-21 14:12:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-617205779  

I am confused about whether to start optimizing multiplication or wait for potential GSoC students to reach there and collaborate with them as it will part of the GSoC. Some students also mentioned that they would implement 3-way Toom-cook.  
  
I don't know, maybe I should wait till the GSoC is over? or should I start in a totally different regime like modular exponentiation or square / nth root.

---

## Comment 30

> Username: ckormanyos  
> Created_at: 2020-04-21 16:28:29 UTC  
> Updated_at: 2020-04-21 16:28:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-617268830  

> I am confused about whether to start optimizing multiplication or wait for potential GSoC students to reach there and collaborate with them as it will part of the GSoC. Some students also mentioned that they would implement 3-way Toom-cook.  
  
> I don't know, maybe I should wait till the GSoC is over?  
  
Thank you for you consideration Madhur.  
  
My wish is that you start exactly where your interest and drive lead you.  
  
My strategy in GSoC is to work in unison with existing progress, whereby the GSoC activities can flexibly be modified depending on the progress being made in the running open source activities.  
  
In short, please do not modify your plans based on GSoC. We will modify our GSoC plans to help fill in the gaps on the running open source work.  
  
Need i provide more details, please don't hesitate to refine your query.  
  
Kind regards, Chris

---

## Comment 31

> Username: jzmaddock  
> Created_at: 2020-04-21 19:24:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/218#issuecomment-617364828  

>My wish is that you start exactly where your interest and drive lead you.  
  
That would be my thought too - there's no great shortage of things to do - and probably never will be!

---
