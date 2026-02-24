# #203 Faster GCD and MOD (single limb) [Merged]

> Username: madhur4127  
> Created at: 2020-03-29 04:50:32 UTC  
> Updated at: 2020-04-13 11:55:12 UTC  
> Merged at: 2020-04-13 11:55:12 UTC  
> Closed at: 2020-04-13 11:55:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203  

Changes Binary GCD to Euclid for first iteration of GCD. This reduces the computation of N x 1 gcd to 1 x 1 gcd, which can be further solved by Binary GCD.  
  
The number of bits (N) of the bigger operand is shown by the numeral in the benchmark name with the type used.  
- 1st  : Original cpp_int  
- 2nd : New cpp_int  
- 3rd : GMP's mpz_int  
  
```  
-----------------------------------------------------------  
Benchmark                 Time             CPU   Iterations  
-----------------------------------------------------------  
BM<cpp_int>/400       10571 ns        10340 ns        68348  
BM<cpp_int>/600       18617 ns        18206 ns        38073  
BM<cpp_int>/800       26179 ns        25998 ns        26051  
BM<cpp_int>/1000      36157 ns        35827 ns        19687  
BM<cpp_int>/1200      45856 ns        45357 ns        15461  
BM<cpp_int>/1400      61913 ns        61316 ns        11331  
BM<cpp_int>/1600      76138 ns        73477 ns         9953  
BM<cpp_int>/1800      98735 ns        92880 ns         8363  
BM<cpp_int>/2000     108352 ns       107191 ns         7081  
  
BM<cpp_int>/400         997 ns          983 ns       710923  
BM<cpp_int>/600        1311 ns         1297 ns       539265  
BM<cpp_int>/800        1561 ns         1546 ns       468111  
BM<cpp_int>/1000       1770 ns         1749 ns       408779  
BM<cpp_int>/1200       1964 ns         1946 ns       342320  
BM<cpp_int>/1400       2286 ns         2237 ns       318498  
BM<cpp_int>/1600       2540 ns         2521 ns       274114  
BM<cpp_int>/1800       3021 ns         2949 ns       247813  
BM<cpp_int>/2000       3082 ns         3059 ns       220344  
  
BM<mpz_int>/400         271 ns          269 ns      2618283  
BM<mpz_int>/600         287 ns          285 ns      2407132  
BM<mpz_int>/800         297 ns          295 ns      2298375  
BM<mpz_int>/1000        331 ns          325 ns      2247878  
BM<mpz_int>/1200        346 ns          337 ns      2138969  
BM<mpz_int>/1400        361 ns          357 ns      2061188  
BM<mpz_int>/1600        548 ns          544 ns      1307383  
BM<mpz_int>/1800        567 ns          563 ns      1260716  
BM<mpz_int>/2000        596 ns          591 ns      1195748  
```  
  
**EDIT:** I erroneously thought to have turned on `O2`

---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-03-29 15:16:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-605652462  

@madhur4127 : Looks like a great improvement. Could you post your benchmark code so I could look at the asm?

---

## Comment 2

> Username: madhur4127  
> Created_at: 2020-03-29 17:07:02 UTC  
> Updated_at: 2020-03-30 08:45:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-605667820  

@NAThompson, sure, here's the code:  
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
   uint64_t                    y = 0x12abcdef12abcdef;  
   for (auto _ : state)  
   {  
	  ++x;  
	  ++y;  
	  benchmark::DoNotOptimize(z = gcd(x, y));  
   }  
}  
int start = 4e2, end = 2e3, step = 2e2;  
BENCHMARK_TEMPLATE(BM, cpp_int)->DenseRange(start, end, step);  
BENCHMARK_TEMPLATE(BM, mpz_int)->DenseRange(start, end, step);  
BENCHMARK_MAIN();  
```  
  
I generally do `git checkout` to get the history and recompile to check the speedup.  
  
---  
One thing I noticed is that GMP's speedup is approximately a constant so that indicates that this is the optimal algorithmic choice and only thing that needs work is constant optimization for modulus operation.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2020-03-29 19:13:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-605685000  

Good catch!  
  
I had to change the int64 to int32 on msvc but then I see:  
  
```  
// Original:  
BM<cpp_int>/400        4884 ns         4883 ns       112000  
BM<cpp_int>/600        9085 ns         8998 ns        74667  
BM<cpp_int>/800       12254 ns        12242 ns        49778  
BM<cpp_int>/1000      17633 ns        17648 ns        40727  
BM<cpp_int>/1200      23719 ns        24065 ns        29867  
BM<cpp_int>/1400      31954 ns        32087 ns        19478  
BM<cpp_int>/1600      38128 ns        38504 ns        18667  
BM<cpp_int>/1800      44976 ns        44643 ns        11200  
BM<cpp_int>/2000      55204 ns        54408 ns        11200  
// Madhurs in C++14:  
BM<cpp_int>/400         452 ns          449 ns      1947826  
BM<cpp_int>/600         607 ns          600 ns      1120000  
BM<cpp_int>/800         560 ns          558 ns      1120000  
BM<cpp_int>/1000        730 ns          732 ns       896000  
BM<cpp_int>/1200        760 ns          767 ns       896000  
BM<cpp_int>/1400        810 ns          802 ns       896000  
BM<cpp_int>/1600        898 ns          900 ns       746667  
BM<cpp_int>/1800       1054 ns         1046 ns       746667  
BM<cpp_int>/2000       1057 ns         1050 ns       640000  
// Madhurs in C++2a (ie has std::gcd):  
BM<cpp_int>/400         451 ns          433 ns      1659259  
BM<cpp_int>/600         514 ns          516 ns      1000000  
BM<cpp_int>/800         552 ns          544 ns      1120000  
BM<cpp_int>/1000        669 ns          670 ns      1120000  
BM<cpp_int>/1200        718 ns          715 ns       896000  
BM<cpp_int>/1400        770 ns          767 ns       896000  
BM<cpp_int>/1600        857 ns          837 ns       896000  
BM<cpp_int>/1800        925 ns          921 ns       746667  
BM<cpp_int>/2000       1012 ns         1001 ns       640000  
// gmp comparison:  
BM<mpz_int>/400         203 ns          199 ns      3446154  
BM<mpz_int>/600         218 ns          214 ns      2986667  
BM<mpz_int>/800         239 ns          240 ns      2800000  
BM<mpz_int>/1000        259 ns          257 ns      2800000  
BM<mpz_int>/1200        267 ns          267 ns      2635294  
BM<mpz_int>/1400        282 ns          285 ns      2635294  
BM<mpz_int>/1600        295 ns          292 ns      2357895  
BM<mpz_int>/1800        295 ns          292 ns      2357895  
BM<mpz_int>/2000        309 ns          307 ns      2240000  
```

---

## Comment 4

> Username: madhur4127  
> Created_at: 2020-03-30 06:42:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-605812755  

> I had to change the int64 to int32 on msvc but then I see:  
  
My bad, I thought I have turned on compiler optimizations. I have updated the new findings. Thanks John for posting your results.  
  
These results look counter-intuitive to me, 400 bits means double the number of limbs in 32bit as opposed to 64 bits. Because modulus is a costly operation it should have been a bottleneck.  
  
Considering the differences in CPU, I could see that the GMP code runs 2 times fast but this PR's code changed speedup from 2 to 3 and the original code runs almost 2 times fast! GMP can be made baseline as it has assembly in most parts.  
  
But like I mentioned, this PR's code has speedup of around 3 while it should run slower in 32 bit! Did speedup increase with an increase in number of operations? Or some compiler tricks are at play here?  
  
Can somebody else share their results?  
  
---  
  
1. Travis' build time reduced by 50 minutes :), from 7:00 hours to 6:10 hours. ([New](https://travis-ci.org/github/boostorg/multiprecision/builds/668422555?utm_source=github_status&utm_medium=notification) vs [Old](https://travis-ci.org/github/boostorg/multiprecision/builds/667438354?utm_medium=notification&utm_source=github_status))  
  
2. The failing tests are the same as that from the `develop`.  
  
3. This also shows that my binary GCD implementation is only a tiny bit slower than that of `std::gcd`. So that won't be an issue.  
  
4. I guess we can reduce the build time substantially if we can optimize gcd.  
  
5. Why isn't Travis build status getting updated here, it already failed on tests that I mentioned in (2).

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2020-03-30 16:34:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-606107179  

Ubunutu/gcc-9.2.1 results:  
  
```  
// Old  
BM<cpp_int>/400        2209 ns       2201 ns     314432  
BM<cpp_int>/600        3843 ns       3837 ns     188131  
BM<cpp_int>/800        5807 ns       5806 ns     121605  
BM<cpp_int>/1000       8341 ns       8309 ns      86047  
BM<cpp_int>/1200      11399 ns      11379 ns      60529  
BM<cpp_int>/1400      14610 ns      14604 ns      46908  
BM<cpp_int>/1600      19701 ns      19693 ns      34387  
BM<cpp_int>/1800      22637 ns      22634 ns      31128  
BM<cpp_int>/2000      28149 ns      28140 ns      24691  
// C++14  
BM<cpp_int>/400         499 ns        496 ns    1386576  
BM<cpp_int>/600         615 ns        615 ns    1145276  
BM<cpp_int>/800         781 ns        781 ns     952585  
BM<cpp_int>/1000        962 ns        962 ns     825334  
BM<cpp_int>/1200        938 ns        938 ns     702271  
BM<cpp_int>/1400       1079 ns       1079 ns     661203  
BM<cpp_int>/1600       1211 ns       1210 ns     579076  
BM<cpp_int>/1800       1340 ns       1340 ns     526301  
BM<cpp_int>/2000       1478 ns       1478 ns     470757  
// C++2a  
BM<cpp_int>/400         686 ns        684 ns     996320  
BM<cpp_int>/600         807 ns        806 ns     879161  
BM<cpp_int>/800         905 ns        904 ns     652798  
BM<cpp_int>/1000       1069 ns       1069 ns     685633  
BM<cpp_int>/1200       1124 ns       1122 ns     612908  
BM<cpp_int>/1400       1238 ns       1236 ns     563427  
BM<cpp_int>/1600       1419 ns       1417 ns     479184  
BM<cpp_int>/1800       1564 ns       1563 ns     418503  
BM<cpp_int>/2000       1654 ns       1654 ns     425399  
BM<mpz_int>/400         155 ns        155 ns    4504794  
BM<mpz_int>/600         160 ns        160 ns    4292198  
BM<mpz_int>/800         166 ns        166 ns    4130676  
BM<mpz_int>/1000        172 ns        172 ns    4072171  
BM<mpz_int>/1200        173 ns        173 ns    3994546  
BM<mpz_int>/1400        175 ns        175 ns    3911749  
BM<mpz_int>/1600        179 ns        179 ns    3898335  
BM<mpz_int>/1800        184 ns        184 ns    3864625  
BM<mpz_int>/2000        187 ns        187 ns    3708390  
```

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2020-03-30 16:40:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-606110200  

With the Intel 19.1 compiler:  
  
```  
// Intel 19.1, Old code:  
BM<cpp_int>/400        5134 ns       5133 ns     129320  
BM<cpp_int>/600        8949 ns       8944 ns      77500  
BM<cpp_int>/800       13105 ns      13104 ns      51726  
BM<cpp_int>/1000      17538 ns      17529 ns      39664  
BM<cpp_int>/1200      22497 ns      22446 ns      31122  
BM<cpp_int>/1400      28309 ns      28236 ns      25233  
BM<cpp_int>/1600      33095 ns      32976 ns      20936  
BM<cpp_int>/1800      38652 ns      38561 ns      17298  
BM<cpp_int>/2000      45565 ns      45563 ns      14901  
// Intel 19.1, C++14  
BM<cpp_int>/400         417 ns        417 ns    1657264  
BM<cpp_int>/600         505 ns        505 ns    1347912  
BM<cpp_int>/800         592 ns        592 ns    1172262  
BM<cpp_int>/1000        677 ns        677 ns    1022318  
BM<cpp_int>/1200        767 ns        767 ns     906172  
BM<cpp_int>/1400        861 ns        861 ns     810206  
BM<cpp_int>/1600        980 ns        980 ns     708451  
BM<cpp_int>/1800       1060 ns       1060 ns     653944  
BM<cpp_int>/2000       1150 ns       1150 ns     604396  
// Intel 19.1, C++2a  
BM<cpp_int>/400         662 ns        662 ns    1027933  
BM<cpp_int>/600         759 ns        758 ns     919607  
BM<cpp_int>/800         850 ns        849 ns     776732  
BM<cpp_int>/1000        908 ns        907 ns     756123  
BM<cpp_int>/1200       1016 ns       1015 ns     682684  
BM<cpp_int>/1400       1142 ns       1142 ns     632162  
BM<cpp_int>/1600       1210 ns       1209 ns     549914  
BM<cpp_int>/1800       1294 ns       1294 ns     539044  
BM<cpp_int>/2000       1382 ns       1382 ns     501860  
BM<mpz_int>/400         154 ns        154 ns    4462814  
BM<mpz_int>/600         161 ns        161 ns    4284225  
BM<mpz_int>/800         167 ns        167 ns    4173778  
BM<mpz_int>/1000        171 ns        171 ns    4067589  
BM<mpz_int>/1200        177 ns        177 ns    4010524  
BM<mpz_int>/1400        181 ns        181 ns    3953872  
BM<mpz_int>/1600        181 ns        181 ns    3893840  
BM<mpz_int>/1800        184 ns        184 ns    3585908  
BM<mpz_int>/2000        194 ns        194 ns    3821018  
```

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2020-03-30 16:55:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-606118389  

>The failing tests are the same as that from the develop.  
  
Somebody committed a file that doesn't actually build: I'll look into it.  
  
> This also shows that my binary GCD implementation is only a tiny bit slower than that of std::gcd. So that won't be an issue.  
  
Tiny bit faster here!  
  
> Why isn't Travis build status getting updated here, it already failed on tests that I mentioned in  
  
I think because this is still marked as a draft PR?

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2020-03-30 17:03:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-606122147  

This looks good, and raises some interesting questions:  
  
1) What happens for gcd with double_limb_type (__int128 for gcc), we could use the same method, but would have to construct a temporary cpp_int for the modulus operator.  
  
2) For the gcd of 2 cpp_int's, does using this procedure help once one of the two has been reduced down to a single limb (or possibly double limb if (1) is a go?  
  
3) For the gcd of 2 cpp_int's, I would normally expect binary gcd to be faster (I'm fairly sure I compared them back in the day, or possibly I'd had enough of coding this by then and gave up too soon!), but what if the 2 integers are very different sizes (numbers of limbs)?  One can imagine an algorithm which uses a Euclid step if the 2 are very different sizes, or a binary step if they are similar.  
  
4) Does gmp have a cunning way of evaluating the modulus to an integer?  We do have a simplified divide+modulus routine for this case, but it might not be optimal for modulus alone?

---

## Comment 9

> Username: madhur4127  
> Created_at: 2020-04-01 13:25:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-607247276  

> What happens for gcd with double_limb_type (__int128 for gcc), we could use the same method, but would have to construct a temporary cpp_int for the modulus operator.  
  
This is easy to handle in a similar fashion, I should probably make it a template function to handle `limb_type` and `double_limb_type` because the assembly for `unsigned __int128_t c = a%b;` is  
  
```assembly  
mod(unsigned __int128, unsigned __int128):  
        sub     rsp, 8  
        call    __umodti3  
        add     rsp, 8  
        ret  
```  
so it is known to the compiler how to efficiently do this. Modulus must also be taken in this way too, like a sliding window for `double_limb_type`. I will have to check what's the current status.  
  
> 2. For the gcd of 2 cpp_int's, does using this procedure help once one of the two has been reduced down to a single limb (or possibly double limb if (1) is a go?  
  
This is where the hard part is, converting a gcd of 2 cpp_ints to a single limb, algorithmically. I believe converting one of them to a single limb or double is not possible without modifying other, so in the end, modulus will be inefficient.  
  
> For the gcd of 2 cpp_int's, I would normally expect binary gcd to be faster (I'm fairly sure I compared them back in the day, or possibly I'd had enough of coding this by then and gave up too soon!), but what if the 2 integers are very different sizes (numbers of limbs)? One can imagine an algorithm which uses a Euclid step if the 2 are very different sizes, or a binary step if they are similar.  
  
Surely binary GCD is faster on approximately similar sizes. I will have to widen my algorithmic toolbox to handle two arbitrary big integers. I am thinking of picking up Knuth's Vol 2 and it will take me some time to fully understand the algorithms to the point where I can efficiently code it up.  
  
> Does gmp have a cunning way of evaluating the modulus to an integer? We do have a simplified divide+modulus routine for this case, but it might not be optimal for modulus alone?  
  
Then we'll have to simplify the modulus, something like this: [Godbolt link](https://godbolt.org/z/2EQUjv).  
> Does GMP have a cunning way of evaluating the modulus to an integer? We do have a simplified divide+modulus routine for this case, but it might not be optimal for modulus alone?  
  
Compilers can also do that, see the `m` function in the above Godbolt link. We can do that using this library: [libDivide](https://libdivide.com).

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2020-04-01 13:59:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-607267112  

> call    __umodti3  
  
Do you remember the strategy I showed you for 32*32 mul -> 64 result? I wonder if you can in this case here for modulus think the same way for the efficient modulus operation?  
  
The assembly you show seems like it might be for a ful-on heavy 128%128 -> 128 result. That might be why the compiler generates potentially time-consuming that calls umod, jumps there, and restores a register or two upon return (slow stuff).  
  
If you seek speed and if possible, coerce the compiler to do the mod of 128 % 64 -> 64, if this satisfies the math requirements in your algoritm (which I do not know iif this is the case).  
  
Kind regards, Chris

---

## Comment 11

> Username: madhur4127  
> Created_at: 2020-04-03 05:54:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-608244322  

> If you seek speed and if possible, coerce the compiler to do the mod of 128 % 64 -> 64, if this satisfies the math requirements in your algorithm (which I do not know iif this is the case).  
  
I don't know how to convert `eval_modulus(cpp_int, double_limb_type)` to 128%64 -> 64.  I don't even know whether this is possible as 128 bit limbs are broken into sum of two 64 limbs. If this would have been a product, I would have used the Chinese Remainder Theorem.

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2020-04-03 07:29:01 UTC  
> Updated_at: 2020-04-03 07:29:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-608276711  

>> If you seek speed and if possible, coerce the compiler to do the mod of 128 % 64 -> 64, if this satisfies the math requirements in your algorithm (which I do not know iif this is the case).  
  
> I don't know how to convert eval_modulus(cpp_int, double_limb_type) to 128%64 -> 64. I don't even know whether this is possible as 128 bit limbs are broken into sum of two 64 limbs. If this would have been a product, I would have used the Chinese Remainder Theorem.  
  
I didn't _really_ know if it would work in your particular use-case either. I just got the possibility on the table in case it was relevant for your case at hand (which I was unsure of).  
  
Best, Chris

---

## Comment 13

> Username: madhur4127  
> Created_at: 2020-04-08 07:53:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-610809094  

AFAICT I think this PR is in good shape for a single limb.  
  
I don't have any optimizations for double limb expect to take modulus and convert both of them to double limbs, this approach is feasible for only for 32bit limbs(MSVC).  
  
I initially thought of converting the double limb modulus to two (or more) single limbs modulus and then combine the results by using the Chinese Remainder Theorem. This approach would be too slow.  
  
The General case for GCD will be handled in a subsequent PR.  
  
Also, the motive of implementing general case first is that it will make it clear whether modulus with a double limb is slower than the general case and we can then decide accordingly.

---

## Comment 14

> Username: madhur4127  
> Created_at: 2020-04-08 08:03:43 UTC  
> Updated_at: 2020-04-08 08:03:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-610814035  

One thing I noticed is that modulus on a single limb can be done in O(number_of_limbs) by using a sliding window technique (similar to rolling hashes) instead of calling generic `divide_unsigned_helper`.  
  
So this specializing this function can improve performance multiple times of GCD, I guess.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2020-04-08 11:01:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-610893207  

I think the sliding window is an excellent idea, I quickly coded up:  
  
```  
boost::multiprecision::limb_type single_limb_mod(const boost::multiprecision::cpp_int& val, boost::multiprecision::limb_type mod)  
{  
   limb_type two_n_mod = 1 + (~static_cast<limb_type>(0u) % mod);  
   limb_type multiplier_mod = 1;  
   limb_type result = val.backend().limbs()[0] % mod;  
  
   for (unsigned i = 1; i < val.backend().size(); ++i)  
   {  
      limb_type limb_mod = val.backend().limbs()[i] % mod;  
      multiplier_mod = static_cast<boost::multiprecision::limb_type>((static_cast<boost::multiprecision::double_limb_type>(multiplier_mod) * two_n_mod) % mod);  
      limb_mod = static_cast<boost::multiprecision::limb_type>((static_cast<boost::multiprecision::double_limb_type>(limb_mod) * multiplier_mod) % mod);  
      result = static_cast<boost::multiprecision::limb_type>((static_cast<boost::multiprecision::double_limb_type>(result) + limb_mod) % mod);  
   }  
   return result;  
}  
```  
  
Which is much better than a full divide and remainder, the casts could also be eluded if we know that the modulus is small enough in value.

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2020-04-08 12:34:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-610932381  

Ha, that code is actually slower than calling eval_modulus! :(

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2020-04-08 15:30:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-611026810  

Best I can come up with for single limb modulus is:  
  
```  
template <unsigned MinBits1, unsigned MaxBits1, cpp_integer_type SignType1, cpp_int_check_type Checked1, class Allocator1>  
limb_type single_limb_mod(const cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1>& val, limb_type mod)  
{  
   limb_type two_n_mod = 1 + (~static_cast<limb_type>(0u) % mod);  
   double_limb_type multiplier_mod = 1;  
   limb_type result = val.limbs()[0] % mod;  
  
   for (unsigned i = 1; i < val.size(); ++i)  
   {  
      multiplier_mod = (multiplier_mod * two_n_mod) % mod;  
      result = static_cast<boost::multiprecision::limb_type>((val.limbs()[i] * multiplier_mod + result) % mod);  
   }  
   return result;  
}  
```  
  
Which has about the same performance as the existing divide routine, @madhur4127 did you have something better in mind?

---

## Comment 18

> Username: madhur4127  
> Created_at: 2020-04-08 15:58:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-611042990  

@jzmaddock, I reduced the modulo operations to N, your code takes 2*N modulo operations. My idea was like horner's rule. Can you run the benchmark on ICC because I think guess ICC can optimize 128-bit modulus operation.

---

## Comment 19

> Username: madhur4127  
> Created_at: 2020-04-08 17:22:58 UTC  
> Updated_at: 2020-04-08 17:25:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-611086378  

I'm clueless how to do 64*64%64, compilers call up `__umodti3` which is for 128%128, I think it can be optimized but I guess we'll need hardware's help.  
  
I observed around 11-16% speedup.  
  
```  
-----------------------------------------------------------  
Benchmark                 Time             CPU   Iterations  
-----------------------------------------------------------  
BM<cpp_int>/400         905 ns          877 ns       793150  
BM<cpp_int>/800        1365 ns         1321 ns       527462  
BM<cpp_int>/1200       1873 ns         1813 ns       377760  
BM<cpp_int>/1600       2274 ns         2210 ns       313245  
BM<cpp_int>/2000       2852 ns         2727 ns       253557  
BM<mpz_int>/400         390 ns          293 ns      2274494  
BM<mpz_int>/800         475 ns          324 ns      2157995  
BM<mpz_int>/1200        363 ns          342 ns      2057975  
BM<mpz_int>/1600        394 ns          384 ns      1884174  
BM<mpz_int>/2000        655 ns          635 ns      1125798  
```  
  
**Edit**: Montgomery Multiplication is a candidate for fixed modulus.

---

## Comment 20

> Username: jzmaddock  
> Created_at: 2020-04-08 17:26:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-611087990  

Bit slower for me with msvc:  
  
```  
Before this change:  
BM<cpp_int>/400         352 ns          345 ns      2036364  
BM<cpp_int>/600         393 ns          394 ns      1544828  
BM<cpp_int>/800         480 ns          454 ns      1445161  
BM<cpp_int>/1000        548 ns          530 ns      1120000  
BM<cpp_int>/1200        627 ns          614 ns      1120000  
BM<cpp_int>/1400        651 ns          645 ns       896000  
BM<cpp_int>/1600        737 ns          732 ns       896000  
BM<cpp_int>/1800        841 ns          837 ns       746667  
BM<cpp_int>/2000        911 ns          858 ns       746667  
After:  
BM<cpp_int>/400         669 ns          656 ns      1120000  
BM<cpp_int>/600         709 ns          698 ns       896000  
BM<cpp_int>/800         821 ns          774 ns       746667  
BM<cpp_int>/1000        759 ns          750 ns       896000  
BM<cpp_int>/1200        847 ns          837 ns       746667  
BM<cpp_int>/1400        928 ns          921 ns       746667  
BM<cpp_int>/1600        976 ns          977 ns       640000  
BM<cpp_int>/1800        955 ns          952 ns       640000  
BM<cpp_int>/2000       1068 ns         1067 ns       746667  
```

---

## Comment 21

> Username: madhur4127  
> Created_at: 2020-04-08 17:32:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-611091291  

There are too many 746667 in the above benchmark with different CPU times!

---

## Comment 22

> Username: jzmaddock  
> Created_at: 2020-04-08 17:40:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-611094915  

Ubuntu results:  
  
```  
Previous results:  
Gcc-9  
M<cpp_int>/400         686 ns        684 ns     996320  
BM<cpp_int>/600         807 ns        806 ns     879161  
BM<cpp_int>/800         905 ns        904 ns     652798  
BM<cpp_int>/1000       1069 ns       1069 ns     685633  
BM<cpp_int>/1200       1124 ns       1122 ns     612908  
BM<cpp_int>/1400       1238 ns       1236 ns     563427  
BM<cpp_int>/1600       1419 ns       1417 ns     479184  
BM<cpp_int>/1800       1564 ns       1563 ns     418503  
BM<cpp_int>/2000       1654 ns       1654 ns     425399  
Intel:  
BM<cpp_int>/400         662 ns        662 ns    1027933  
BM<cpp_int>/600         759 ns        758 ns     919607  
BM<cpp_int>/800         850 ns        849 ns     776732  
BM<cpp_int>/1000        908 ns        907 ns     756123  
BM<cpp_int>/1200       1016 ns       1015 ns     682684  
BM<cpp_int>/1400       1142 ns       1142 ns     632162  
BM<cpp_int>/1600       1210 ns       1209 ns     549914  
BM<cpp_int>/1800       1294 ns       1294 ns     539044  
BM<cpp_int>/2000       1382 ns       1382 ns     501860  
  
New results  
gcc-9:  
BM<cpp_int>/400         689 ns        669 ns    1041691  
BM<cpp_int>/600         780 ns        773 ns     922279  
BM<cpp_int>/800         867 ns        867 ns     792103  
BM<cpp_int>/1000        961 ns        961 ns     714568  
BM<cpp_int>/1200       1117 ns       1117 ns     652278  
BM<cpp_int>/1400       1184 ns       1184 ns     572846  
BM<cpp_int>/1600       1308 ns       1308 ns     531422  
BM<cpp_int>/1800       1516 ns       1516 ns     470111  
BM<cpp_int>/2000       1559 ns       1559 ns     445317  
  
Intel:  
BM<cpp_int>/400         634 ns        633 ns    1110774  
BM<cpp_int>/600         711 ns        710 ns     976036  
BM<cpp_int>/800         779 ns        779 ns     885377  
BM<cpp_int>/1000        883 ns        879 ns     792514  
BM<cpp_int>/1200        941 ns        941 ns     728018  
BM<cpp_int>/1400       1023 ns       1023 ns     669629  
BM<cpp_int>/1600       1128 ns       1128 ns     625539  
BM<cpp_int>/1800       1212 ns       1212 ns     573532  
BM<cpp_int>/2000       1292 ns       1286 ns     539160  
```  
  
So about the same?

---

## Comment 23

> Username: madhur4127  
> Created_at: 2020-04-08 17:55:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-611102423  

> So about the same?  
  
Yes, they look pretty similar. I used `clang++-9.0.1 -O2` though for those benchmarks.  
  
It's funny how O(N) time O(1) space lost because of a single operation. I hope this will get better in future :)  
  
The most curious case is of MSVC where 32*32%64bit modulo must have been done in native hardware instructions.

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2020-04-09 14:27:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-611557768  

The algorithm you have there looks the "right way" to me, and with a bit of jiggling around I think I have about 30% knocked off the times, bare with me a bit while I do some more testing though...

---

## Comment 25

> Username: madhur4127  
> Created_at: 2020-04-09 17:39:55 UTC  
> Updated_at: 2020-04-09 17:42:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-611658125  

> bare with me a bit while I do some more testing though...  
  
This version is also untested after the modular commits. I also plan to stress it.   
~I am making this PR  draft until I verify the correctness.~ This feature doesn't exist.

---

## Comment 26

> Username: jzmaddock  
> Created_at: 2020-04-09 18:09:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-611674052  

Those last 2 commits cause massive failures for me locally - I doubt very much that line is performance critical anyway (even if that approach would be more elegant)?

---

## Comment 27

> Username: jzmaddock  
> Created_at: 2020-04-09 20:03:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-611726350  

@madhur4127 : I've fixed some test failures, removed some tabs and other SNAFU's, but also streamlined the code slightly (fewer temporaries and fewer function calls) in this: https://github.com/boostorg/multiprecision/pull/215.  Can you performance compare to what you have now?  I make it about a third quicker, mostly through fewer temporaries.

---

## Comment 28

> Username: madhur4127  
> Created_at: 2020-04-10 08:38:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/203#issuecomment-611940215  

> I've fixed some test failures, removed some tabs and other SNAFU's,  
  
@jzmaddock, My apologies, It was very late when I pushed those commits. I wanted to resume work in the morning so that was a sort of reminder.  
  
> Can you performance compare to what you have now? I make it about a third quicker, mostly through fewer temporaries.  
  
I see a 10% improvement from yesterday, so I went ahead and removed the temporary completely.  
  
```  
gcc 9.2.0 -O2  
// John: faster_gcd_and_mod  
-----------------------------------------------------------  
Benchmark                 Time             CPU   Iterations  
-----------------------------------------------------------  
BM<cpp_int>/500         472 ns          444 ns      1155601  
BM<cpp_int>/1000        715 ns          692 ns      1018803  
BM<cpp_int>/1500        996 ns          974 ns       751584  
BM<cpp_int>/2000       1264 ns         1250 ns       524189  
BM<cpp_int>/2500       1573 ns         1565 ns       467745  
BM<cpp_int>/3000       1778 ns         1768 ns       416317  
  
// Madhur: madhur4127/develop : Fixed errors, Yesterday  
BM<cpp_int>/500         507 ns          497 ns      1435207  
BM<cpp_int>/1000        791 ns          781 ns       895671  
BM<cpp_int>/1500       1034 ns         1023 ns       661717  
BM<cpp_int>/2000       1332 ns         1318 ns       530178  
BM<cpp_int>/2500       1750 ns         1733 ns       420329  
BM<cpp_int>/3000       1971 ns         1955 ns       338987  
  
// Madhur: madhur4127/develop : Remove temporary, Today  
BM<cpp_int>/500         412 ns          408 ns      1700194  
BM<cpp_int>/1000        684 ns          668 ns      1044978  
BM<cpp_int>/1500        965 ns          957 ns       756048  
BM<cpp_int>/2000       1189 ns         1182 ns       593781  
BM<cpp_int>/2500       1514 ns         1480 ns       487525  
BM<cpp_int>/3000       1715 ns         1684 ns       420721  
  
BM<mpz_int>/1000        156 ns          153 ns      4737224  
BM<mpz_int>/1500        165 ns          165 ns      4195695  
BM<mpz_int>/2000        288 ns          285 ns      2504962  
BM<mpz_int>/2500        312 ns          311 ns      2289293  
BM<mpz_int>/3000        355 ns          353 ns      2027263  
```

---
