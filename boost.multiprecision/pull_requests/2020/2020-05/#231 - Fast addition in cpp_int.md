# #231 Fast addition in cpp_int [Closed]

> Username: madhur4127  
> Created at: 2020-05-03 05:02:45 UTC  
> Updated at: 2020-05-28 12:41:59 UTC  
> Closed at: 2020-05-28 12:41:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231  

Compilers currently generate *bad* assembly so, with a little help to the compiler, I was able to reduce the number of instructions (`mov` and `add` both) in the addition loop.   
  
[See the Godbolt link showing the difference in assembly](https://godbolt.org/z/lH1_Hc)  
  
Benefits:  
- Can be easily unrolled  
- Easy to read  
- Efficient (?)  
  
```  
// clang 10.0, Arch Linux, -std=c++11  
// Current develop  
BM<bst_int>/40000        3826 ns         3730 ns       191511  
BM<bst_int>/80000        4545 ns         4486 ns       157061  
BM<bst_int>/120000       5416 ns         5335 ns       135902  
BM<bst_int>/160000       6238 ns         6139 ns       113651  
BM<bst_int>/200000       6924 ns         6829 ns       103996  
  
// This PR  
BM<bst_int>/40000        3395 ns         3362 ns       208408  
BM<bst_int>/80000        3954 ns         3921 ns       172037  
BM<bst_int>/120000       4551 ns         4511 ns       158986  
BM<bst_int>/160000       5120 ns         5063 ns       136567  
BM<bst_int>/200000       5545 ns         5496 ns       112399  
  
BM<mpz_int>/40000         751 ns          744 ns       961070  
BM<mpz_int>/80000        1536 ns         1522 ns       461803  
BM<mpz_int>/120000       2389 ns         2371 ns       297695  
BM<mpz_int>/160000       3475 ns         3439 ns       215895  
BM<mpz_int>/200000       4008 ns         3972 ns       178096  
```  
  
Concerns:  
- Most importantly will boost take this path of using intrinsics?  
- Still unable to see huge performance gains as `mov` instructions are reduced and also `add` is executed only once instead of 4 times. Needs some investigation.  
- GCC cannot unroll the loop while clang can, thus there is a huge disparity between GCC & LLVM.  
- This assembly can be optimized further by writing assembly by hand (inline asm?) but still has potential.  
- Portability [New!]

---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-05-03 10:08:39 UTC  
> Updated_at: 2020-05-03 10:21:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-623085815  

> Most importantly will boost take this path of using intrinsics?  
  
Is there a macro that can detect the presence of the intrinsic? If so it looks like a pretty unambiguous win.  
  
BTW how is `mpz_int` so fast? What does its asm look like?

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2020-05-03 10:23:25 UTC  
> Updated_at: 2020-05-03 10:23:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-623087591  

Have you tried to change the original bottleneck line to:  
  
`carry += static_cast<double_limb_type>(static_cast<double_limb_type>(*pa) + *pb);`  
  
It looks like the disassembled code might be performing ful (double-limb + double-limb) add, which would in fact be slow.  
  
I did not chek this. But you might consider to do so.  
  
Kind regards, Chris

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2020-05-03 10:43:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-623089951  

You could even try:  
```  
carry += *pa;  
carry += *pb;  
```  
... to get oriented with how these particular compilers might _think_, which I also did not actually do. So just an idea here...

---

## Comment 4

> Username: madhur4127  
> Created_at: 2020-05-03 10:57:55 UTC  
> Updated_at: 2020-05-03 11:01:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-623091501  

> carry += *pa;  
> carry += *pb;  
  
This should take 4 64 bit add instructions to execute. My version generates only 1  without the need for double limb.  
  
> BTW how is mpz_int so fast? What does its asm look like?  
  
LLVM with support from Intel recently announced _ExtInt which generates most efficient assembly, you may look at that.  
  
Still few instructions can be optimised if I could use ADCX instruction.  
  
I will have to rerun the benchmark, I used `x+x` and I guess GMP optimised it to 2*x.  
  
Oops, I didn't mean to close the PR (AFK, on cellphone).

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2020-05-03 12:42:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-623104295  

@madhur4127 the compiler intrinsic corresponding to ADDCX is:  
  
```  
unsigned char _addcarryx_u64 (unsigned char c_in, unsigned __int64 src1, unsigned __int64 src2, unsigned __int64 *sum_out);  
```  
  
@NAThompson : yes we can configure this, though it will take a bit of headscratching ;)  I can take care of that if @madhur4127 can optimise this as far as it's likely to go.

---

## Comment 6

> Username: madhur4127  
> Created_at: 2020-05-04 06:16:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-623277994  

> @madhur4127 the compiler intrinsic corresponding to ADDCX is:  
  
This cannot be made to work without ask. Because:  
1. GCC doesn't recognize CF and OF carry flag so ADOX, ADCX cannot be made to work at all.   
2. It's hard to fight the compiler to generate the assembly that I want, it is not possible at all.  
3. Even though ICC supports it, the generated assembly is ridiculous.  
  
So the Intel's ADX is out of the equation. Its sad to see Intel adding two instructions to support big integer arithmetic without making it *easy* enough to use it.  
  
>  yes we can configure this, though it will take a bit of headscratching ;) I can take care of that if @madhur4127 can optimise this as far as it's likely to go.  
   
I am able to generate an almost optimal assembly with Clang with negligible overhead (1  per 22 instructions in the loop). This the best that can be done. [See Godbolt for assembly](https://godbolt.org/z/tCDVy9).  
  
This PR will use `ADC` which has been part of ISA since the times of 8086 and is present in every processor and architecture. So no head-scratching I guess ([See Intel's intrinsic guide](https://software.intel.com/sites/landingpage/IntrinsicsGuide/#text=_addcarry_u64)).  
  
Here is the comparison with GMP. There are many more things at play here that might have caused the difference and I am confident (now) that addition is not responsible for it.  
  
GCC cannot optimize the way that Clang did, this is a missed optimization bug which I will file soon. It generated bad assembly before and now it has improved (but still is too slow as compared to clang).  
  
  
```  
// Current develop  
BM<bst_int>/40000        3826 ns         3730 ns       191511  
BM<bst_int>/80000        4545 ns         4486 ns       157061  
BM<bst_int>/120000       5416 ns         5335 ns       135902  
BM<bst_int>/160000       6238 ns         6139 ns       113651  
BM<bst_int>/200000       6924 ns         6829 ns       103996  
  
// This PR  
BM<bst_int>/40000        3395 ns         3362 ns       208408  
BM<bst_int>/80000        3954 ns         3921 ns       172037  
BM<bst_int>/120000       4551 ns         4511 ns       158986  
BM<bst_int>/160000       5120 ns         5063 ns       136567  
BM<bst_int>/200000       5545 ns         5496 ns       112399  
  
BM<mpz_int>/40000         751 ns          744 ns       961070  
BM<mpz_int>/80000        1536 ns         1522 ns       461803  
BM<mpz_int>/120000       2389 ns         2371 ns       297695  
BM<mpz_int>/160000       3475 ns         3439 ns       215895  
BM<mpz_int>/200000       4008 ns         3972 ns       178096  
```

---

## Comment 7

> Username: NAThompson  
> Created_at: 2020-05-04 11:13:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-623402665  

@madhur4127 : What are your compiler flags? Looking at your godbolt, you might benefit from `-march=native`, or (on goldbolt) `-march=skylake`.

---

## Comment 8

> Username: madhur4127  
> Created_at: 2020-05-04 11:30:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-623409505  

> What are your compiler flags?  
  
`-O3 -march=native`  
  
> Looking at your godbolt, you might benefit from -march=native, or (on goldbolt) -march=skylake.  
  
I have a coffee lake CPU and `-march=native` or `-march=skylake` made no difference. In fact I guess, there shouldn't be any difference as the instructions used are add, adc, lea, mov. Which I guess every processor of this century will support.  
  
Here's the code used in benchmarking:  
```c++  
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
   mt19937_64                  mt(223);  
   int                         bits = state.range(0);  
   uniform_int_distribution<T> ui((T(1) << (bits - 1) + 1), T(1) << bits);  
   T                           x = ui(mt), y = ui(mt), z;  
  
   for (auto _ : state)  
   {  
	  benchmark::DoNotOptimize(z = x + y);  
	  x++;  
   }  
}  
const int start = 4e4, end = 2e5, step = 4e4;  
using bst_int = number<cpp_int_backend<2 * end, 2 * end, unsigned_magnitude, unchecked>, et_off>;  
BENCHMARK_TEMPLATE(BM, bst_int)->DenseRange(start, end, step);  
BENCHMARK_TEMPLATE(BM, mpz_int)->DenseRange(start, end, step);  
BENCHMARK_MAIN();  
  
```

---

## Comment 9

> Username: madhur4127  
> Created_at: 2020-05-04 11:41:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-623414282  

The difference between GCC/ICC vs Clang is very much significant.  
  
I think as per performance is concerned, there is an improvement over popular compilers (MSVC?).  
  
```  
Flags: -O3 -march=native  
  
// GCC (gcc version 9.3.0 (Arch Linux 9.3.0-1)), Old:  
BM<bst_int>/40000        8290 ns         6509 ns        97295  
BM<bst_int>/80000        8039 ns         7914 ns        87575  
BM<bst_int>/120000      10061 ns         9873 ns        70388  
BM<bst_int>/160000      12735 ns        12250 ns        58322  
BM<bst_int>/200000      14449 ns        13855 ns        51252  
  
//GCC, New:  
BM<bst_int>/40000        5413 ns         5350 ns       117355  
BM<bst_int>/80000        6715 ns         6538 ns       104644  
BM<bst_int>/120000      10042 ns         7391 ns        91100  
BM<bst_int>/160000       8398 ns         8116 ns        79428  
BM<bst_int>/200000       8839 ns         8727 ns        80258  
  
// ICC(icc version 19.1.1.219 (gcc version 9.3.0 compatibility)), Old  
BM<bst_int>/40000        9426 ns         9330 ns        73675  
BM<bst_int>/80000       10277 ns        10204 ns        66700  
BM<bst_int>/120000      11353 ns        11282 ns        59955  
BM<bst_int>/160000      11947 ns        11863 ns        56598  
BM<bst_int>/200000      19063 ns        14264 ns        55008  
  
// ICC, New  
BM<bst_int>/40000        8688 ns         8471 ns        64066  
BM<bst_int>/80000        9894 ns         9764 ns        73916  
BM<bst_int>/120000      10460 ns        10364 ns        67272  
BM<bst_int>/160000      11647 ns        11491 ns        61775  
BM<bst_int>/200000      12290 ns        12179 ns        57930  
```

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2020-05-04 17:20:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-623594190  

I see much the same results as @madhur4127 on Mingw-msys-64.  
  
GCC-9:  
  
```  
--------------------------------------------------------------------------------  
Benchmark                                      Time             CPU   Iterations  
--------------------------------------------------------------------------------  
BM_old<cpp_int>/1024                       42793 ns        42516 ns        19478  
BM_old<cpp_int>/2048                       67584 ns        68359 ns        11200  
BM_old<cpp_int>/4096                      126935 ns       125558 ns         5600  
BM_old<cpp_int>/8192                      238956 ns       231262 ns         2635  
BM_old<cpp_int>/16384                     527264 ns       531250 ns         1000  
BM_old<cpp_int>/32768                    1032572 ns      1025391 ns          640  
BM_old<cpp_int>/65536                    1995604 ns      2001953 ns          320  
BM_addc_no_unroll<cpp_int>/1024            29969 ns        30134 ns        24889  
BM_addc_no_unroll<cpp_int>/2048            52946 ns        53013 ns        11200  
BM_addc_no_unroll<cpp_int>/4096            85174 ns        85794 ns         7467  
BM_addc_no_unroll<cpp_int>/8192           143492 ns       142997 ns         4480  
BM_addc_no_unroll<cpp_int>/16384          446624 ns       445113 ns         2036  
BM_addc_no_unroll<cpp_int>/32768          881954 ns       903320 ns          640  
BM_addc_no_unroll<cpp_int>/65536         1820469 ns      1843164 ns          373  
BM_addc_unroll_times_4<cpp_int>/1024       28235 ns        28250 ns        24889  
BM_addc_unroll_times_4<cpp_int>/2048       45913 ns        46039 ns        14933  
BM_addc_unroll_times_4<cpp_int>/4096       83938 ns        83702 ns         7467  
BM_addc_unroll_times_4<cpp_int>/8192      173034 ns       172631 ns         4073  
BM_addc_unroll_times_4<cpp_int>/16384     417814 ns       423825 ns         1659  
BM_addc_unroll_times_4<cpp_int>/32768     857495 ns       857597 ns          747  
BM_addc_unroll_times_4<cpp_int>/65536    1537910 ns      1535627 ns          407  
BM_adcx_unroll_times_4<cpp_int>/1024       23188 ns        22879 ns        28000  
BM_adcx_unroll_times_4<cpp_int>/2048       38956 ns        38504 ns        18667  
BM_adcx_unroll_times_4<cpp_int>/4096       69749 ns        69754 ns         8960  
BM_adcx_unroll_times_4<cpp_int>/8192      135489 ns       133929 ns         5600  
BM_adcx_unroll_times_4<cpp_int>/16384     293258 ns       291561 ns         2358  
BM_adcx_unroll_times_4<cpp_int>/32768     700856 ns       697545 ns         1120  
BM_adcx_unroll_times_4<cpp_int>/65536    1491634 ns      1474649 ns          498  
BM_old<mpz_int>/1024                       19961 ns        19531 ns        32000  
BM_old<mpz_int>/2048                       29945 ns        29994 ns        22400  
BM_old<mpz_int>/4096                       50325 ns        50000 ns        10000  
BM_old<mpz_int>/8192                       93172 ns        94164 ns         7467  
BM_old<mpz_int>/16384                     338233 ns       338992 ns         2489  
BM_old<mpz_int>/32768                     666361 ns       662667 ns          896  
BM_old<mpz_int>/65536                    1384943 ns      1411898 ns          498  
```  
  
clang-9:  
  
```  
--------------------------------------------------------------------------------  
Benchmark                                      Time             CPU   Iterations  
--------------------------------------------------------------------------------  
BM_old<cpp_int>/1024                       26212 ns        26088 ns        26353  
BM_old<cpp_int>/2048                       40607 ns        40806 ns        17231  
BM_old<cpp_int>/4096                       67261 ns        66964 ns        11200  
BM_old<cpp_int>/8192                      126937 ns       125552 ns         4978  
BM_old<cpp_int>/16384                     361121 ns       366211 ns         1792  
BM_old<cpp_int>/32768                     821307 ns       836680 ns          747  
BM_old<cpp_int>/65536                    1687732 ns      1689189 ns          407  
BM_addc_no_unroll<cpp_int>/1024            18289 ns        18415 ns        37333  
BM_addc_no_unroll<cpp_int>/2048            30100 ns        29820 ns        23579  
BM_addc_no_unroll<cpp_int>/4096            52018 ns        53125 ns        10000  
BM_addc_no_unroll<cpp_int>/8192           114407 ns       114397 ns         5600  
BM_addc_no_unroll<cpp_int>/16384          322222 ns       322316 ns         2133  
BM_addc_no_unroll<cpp_int>/32768          761221 ns       767299 ns          896  
BM_addc_no_unroll<cpp_int>/65536         1629159 ns      1574017 ns          407  
BM_addc_unroll_times_4<cpp_int>/1024       21882 ns        21972 ns        29867  
BM_addc_unroll_times_4<cpp_int>/2048       43629 ns        42619 ns        17231  
BM_addc_unroll_times_4<cpp_int>/4096       66402 ns        65569 ns        11200  
BM_addc_unroll_times_4<cpp_int>/8192      103620 ns       102539 ns         6400  
BM_addc_unroll_times_4<cpp_int>/16384     237739 ns       240626 ns         2987  
BM_addc_unroll_times_4<cpp_int>/32768     644024 ns       641741 ns         1120  
BM_addc_unroll_times_4<cpp_int>/65536    1433401 ns      1443273 ns          498  
BM_adcx_unroll_times_4<cpp_int>/1024       23290 ns        23542 ns        29867  
BM_adcx_unroll_times_4<cpp_int>/2048       35040 ns        34494 ns        19478  
BM_adcx_unroll_times_4<cpp_int>/4096       56070 ns        55804 ns        11200  
BM_adcx_unroll_times_4<cpp_int>/8192       99245 ns        97656 ns         6400  
BM_adcx_unroll_times_4<cpp_int>/16384     238834 ns       239955 ns         2800  
BM_adcx_unroll_times_4<cpp_int>/32768     654872 ns       662667 ns          896  
BM_adcx_unroll_times_4<cpp_int>/65536    1657995 ns      1650799 ns          407  
BM_old<mpz_int>/1024                       21685 ns        21973 ns        32000  
BM_old<mpz_int>/2048                       30569 ns        30692 ns        22400  
BM_old<mpz_int>/4096                       51198 ns        51562 ns        10000  
BM_old<mpz_int>/8192                       94812 ns        96257 ns         7467  
BM_old<mpz_int>/16384                     189299 ns       188316 ns         2987  
BM_old<mpz_int>/32768                     576193 ns       571987 ns         1120  
BM_old<mpz_int>/65536                    1283305 ns      1283482 ns          560  
```  
  
The test code is:  
  
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
         inline void add_unsigned_new_1(CppInt1& result, const CppInt2& a, const CppInt3& b) BOOST_MP_NOEXCEPT_IF(is_non_throwing_cpp_int<CppInt1>::value)  
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
            unsigned      i;  
            unsigned char carry = 0;  
            for (i = 0; i < m; ++i)  
            {  
               carry = _addcarry_u64(carry, pa[i], pb[i], &pr[i]);  
            }  
            for (; i < x && carry; ++i)  
               carry = _addcarry_u64(carry, pa[i], 0, &pr[i]);  
            if (i == x && carry)  
            {  
               // We overflowed, need to add one more limb:  
               result.resize(x + 1, x + 1);  
               if (result.size() > x)  
                  result.limbs()[x] = carry;  
            }  
            else  
               std_constexpr::copy(pa + i, pa + x, pr + i);  
            result.normalize();  
            result.sign(a.sign());  
         }  
  
         template <class CppInt1, class CppInt2, class CppInt3>  
         inline void add_unsigned_new_2(CppInt1& result, const CppInt2& a, const CppInt3& b) BOOST_MP_NOEXCEPT_IF(is_non_throwing_cpp_int<CppInt1>::value)  
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
            unsigned      i;  
            unsigned char carry = 0;  
            for (i = 0; i + 4 <= m; i += 4)  
            {  
               carry = _addcarry_u64(carry, pa[i + 0], pb[i + 0], &pr[i + 0]);  
               carry = _addcarry_u64(carry, pa[i + 1], pb[i + 1], &pr[i + 1]);  
               carry = _addcarry_u64(carry, pa[i + 2], pb[i + 2], &pr[i + 2]);  
               carry = _addcarry_u64(carry, pa[i + 3], pb[i + 3], &pr[i + 3]);  
            }  
            for (; i < m; ++i)  
               carry = _addcarry_u64(carry, pa[i], pb[i], &pr[i]);  
            for (; i < x && carry; ++i)  
               carry = _addcarry_u64(carry, pa[i], 0, &pr[i]);  
            if (i == x && carry)  
            {  
               // We overflowed, need to add one more limb:  
               result.resize(x + 1, x + 1);  
               if (result.size() > x)  
                  result.limbs()[x] = carry;  
            }  
            else  
               std_constexpr::copy(pa + i, pa + x, pr + i);  
            result.normalize();  
            result.sign(a.sign());  
         }  
         template <class CppInt1, class CppInt2, class CppInt3>  
         inline void add_unsigned_new_3(CppInt1& result, const CppInt2& a, const CppInt3& b) BOOST_MP_NOEXCEPT_IF(is_non_throwing_cpp_int<CppInt1>::value)  
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
            unsigned      i;  
            unsigned char carry = 0;  
            for (i = 0; i + 4 <= m; i += 4)  
            {  
               carry = _addcarryx_u64(carry, pa[i + 0], pb[i + 0], &pr[i + 0]);  
               carry = _addcarryx_u64(carry, pa[i + 1], pb[i + 1], &pr[i + 1]);  
               carry = _addcarryx_u64(carry, pa[i + 2], pb[i + 2], &pr[i + 2]);  
               carry = _addcarryx_u64(carry, pa[i + 3], pb[i + 3], &pr[i + 3]);  
            }  
            for (; i < m; ++i)  
               carry = _addcarryx_u64(carry, pa[i], pb[i], &pr[i]);  
            for (; i < x && carry; ++i)  
               carry = _addcarryx_u64(carry, pa[i], 0, &pr[i]);  
            if (i == x && carry)  
            {  
               // We overflowed, need to add one more limb:  
               result.resize(x + 1, x + 1);  
               if (result.size() > x)  
                  result.limbs()[x] = carry;  
            }  
            else  
               std_constexpr::copy(pa + i, pa + x, pr + i);  
            result.normalize();  
            result.sign(a.sign());  
         }  
  
      }  
   }  
}  
  
template <typename T>  
static void BM_old(benchmark::State& state)  
{  
   mt19937_64                  mt;  
   int                         bits = state.range(0);  
   uniform_int_distribution<T> ui((T(1) << (bits - 1) + 1), T(1) << bits);  
  
   std::vector<T> a, b, c;  
   for (unsigned i = 0; i < 1000; ++i)  
   {  
      a.push_back(ui(mt));  
      b.push_back(ui(mt));  
      c.push_back(0);  
   }  
  
  
  
   for (auto _ : state)  
   {  
      for (unsigned i = 0; i < a.size(); ++i)  
         eval_add(c[i].backend(), a[i].backend(), b[i].backend());  
   }  
}  
  
template <typename T>  
static void BM_addc_no_unroll(benchmark::State& state)  
{  
   mt19937_64                  mt;  
   int                         bits = state.range(0);  
   uniform_int_distribution<T> ui((T(1) << (bits - 1) + 1), T(1) << bits);  
  
   std::vector<T> a, b, c;  
   for (unsigned i = 0; i < 1000; ++i)  
   {  
      a.push_back(ui(mt));  
      b.push_back(ui(mt));  
      c.push_back(0);  
   }  
  
  
  
   for (auto _ : state)  
   {  
      for (unsigned i = 0; i < a.size(); ++i)  
         add_unsigned_new_1(c[i].backend(), a[i].backend(), b[i].backend());  
   }  
}  
  
template <typename T>  
static void BM_addc_unroll_times_4(benchmark::State& state)  
{  
   mt19937_64                  mt;  
   int                         bits = state.range(0);  
   uniform_int_distribution<T> ui((T(1) << (bits - 1) + 1), T(1) << bits);  
  
   std::vector<T> a, b, c;  
   for (unsigned i = 0; i < 1000; ++i)  
   {  
      a.push_back(ui(mt));  
      b.push_back(ui(mt));  
      c.push_back(0);  
   }  
  
  
  
   for (auto _ : state)  
   {  
      for (unsigned i = 0; i < a.size(); ++i)  
         add_unsigned_new_2(c[i].backend(), a[i].backend(), b[i].backend());  
   }  
}  
  
template <typename T>  
static void BM_adcx_unroll_times_4(benchmark::State& state)  
{  
   mt19937_64                  mt;  
   int                         bits = state.range(0);  
   uniform_int_distribution<T> ui((T(1) << (bits - 1) + 1), T(1) << bits);  
  
   std::vector<T> a, b, c;  
   for (unsigned i = 0; i < 1000; ++i)  
   {  
      a.push_back(ui(mt));  
      b.push_back(ui(mt));  
      c.push_back(0);  
   }  
  
  
  
   for (auto _ : state)  
   {  
      for (unsigned i = 0; i < a.size(); ++i)  
         add_unsigned_new_3(c[i].backend(), a[i].backend(), b[i].backend());  
   }  
}  
  
BENCHMARK_TEMPLATE(BM_old, cpp_int)->RangeMultiplier(2)->Range(1024, 1 << 16);  
BENCHMARK_TEMPLATE(BM_addc_no_unroll, cpp_int)->RangeMultiplier(2)->Range(1024, 1 << 16);  
BENCHMARK_TEMPLATE(BM_addc_unroll_times_4, cpp_int)->RangeMultiplier(2)->Range(1024, 1 << 16);  
BENCHMARK_TEMPLATE(BM_adcx_unroll_times_4, cpp_int)->RangeMultiplier(2)->Range(1024, 1 << 16);  
BENCHMARK_TEMPLATE(BM_old, mpz_int)->RangeMultiplier(2)->Range(1024, 1 << 16);  
  
BENCHMARK_MAIN();  
```

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2020-05-04 17:51:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-623610099  

Here's the MSVC-14.2 results, obviously the *_u36 intrinsics were changed to *_u32 calls:  
  
```  
--------------------------------------------------------------------------------  
Benchmark                                      Time             CPU   Iterations  
--------------------------------------------------------------------------------  
BM_old<cpp_int>/1024                       46403 ns        46141 ns        16593  
BM_old<cpp_int>/2048                       79516 ns        79517 ns         7467  
BM_old<cpp_int>/4096                      160526 ns       161122 ns         4073  
BM_old<cpp_int>/8192                      348133 ns       337945 ns         2358  
BM_old<cpp_int>/16384                    1108159 ns       976563 ns          640  
BM_old<cpp_int>/32768                    1532451 ns      1499721 ns          448  
BM_old<cpp_int>/65536                    3104138 ns      2949297 ns          249  
BM_addc_no_unroll<cpp_int>/1024            66301 ns        66267 ns         8960  
BM_addc_no_unroll<cpp_int>/2048           113095 ns       106720 ns         7467  
BM_addc_no_unroll<cpp_int>/4096           185629 ns       184168 ns         3733  
BM_addc_no_unroll<cpp_int>/8192           297804 ns       299300 ns         2036  
BM_addc_no_unroll<cpp_int>/16384          618742 ns       609375 ns         1000  
BM_addc_no_unroll<cpp_int>/32768         1119585 ns      1116071 ns          560  
BM_addc_no_unroll<cpp_int>/65536         2152872 ns      2173913 ns          345  
BM_addc_unroll_times_4<cpp_int>/1024       34795 ns        34528 ns        20364  
BM_addc_unroll_times_4<cpp_int>/2048       56163 ns        56250 ns        10000  
BM_addc_unroll_times_4<cpp_int>/4096       99514 ns        98349 ns         7467  
BM_addc_unroll_times_4<cpp_int>/8192      196103 ns       194972 ns         3446  
BM_addc_unroll_times_4<cpp_int>/16384     572854 ns       562500 ns         1000  
BM_addc_unroll_times_4<cpp_int>/32768    1135229 ns      1098143 ns          498  
BM_addc_unroll_times_4<cpp_int>/65536    1889381 ns      1902174 ns          345  
BM_adcx_unroll_times_4<cpp_int>/1024       36395 ns        36830 ns        20364  
BM_adcx_unroll_times_4<cpp_int>/2048       56301 ns        55804 ns        11200  
BM_adcx_unroll_times_4<cpp_int>/4096       82468 ns        83702 ns         7467  
BM_adcx_unroll_times_4<cpp_int>/8192      150509 ns       149972 ns         4480  
BM_adcx_unroll_times_4<cpp_int>/16384     324125 ns       322316 ns         2133  
BM_adcx_unroll_times_4<cpp_int>/32768     768959 ns       767299 ns          896  
BM_adcx_unroll_times_4<cpp_int>/65536    1575265 ns      1569475 ns          448  
BM_old<mpz_int>/1024                       30420 ns        30483 ns        23579  
BM_old<mpz_int>/2048                       48334 ns        48652 ns        14452  
BM_old<mpz_int>/4096                       86442 ns        87193 ns         8960  
BM_old<mpz_int>/8192                      165666 ns       164958 ns         4073  
BM_old<mpz_int>/16384                     365418 ns       360947 ns         1948  
BM_old<mpz_int>/32768                     761142 ns       753012 ns          747  
BM_old<mpz_int>/65536                    1609203 ns      1612408 ns          407  
```

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2020-05-04 17:51:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-623610426  

Ugh, I meant _u64.

---

## Comment 13

> Username: madhur4127  
> Created_at: 2020-05-04 18:15:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-623623017  

> BM_adcx_unroll_times_4<cpp_int>/65536    1575265 ns      1569475 ns          448  
> BM_old<mpz_int>/65536   1609203 ns      1612408 ns          407  
  
I'm amazed! Faster than GMP, on MSVC?  
  
> Here's the MSVC-14.2 results, obviously the *_u36 intrinsics were changed to *_u32 calls:  
  
@jzmaddock, MSVC also supports 64bit ADC, so why not use 64bit limbs instead? 4 times unrolling of 64bit would mean processing of 8 32bit limbs in an iteration instead. That should be very fast (comparatively)!

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2020-05-05 07:48:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-623908455  

>I'm amazed! Faster than GMP, on MSVC?  
  
It's not a fair comparison - I'm using the generic (no assembler) MPIR build to simplify the build.  
  
>MSVC also supports 64bit ADC, so why not use 64bit limbs instead? 4 times unrolling of 64bit would mean processing of 8 32bit limbs in an iteration instead. That should be very fast (comparatively)!  
  
I can't in the general case use 64-bit limbs since there's no __int128 on msvc.  I could probably reinterpret_cast the 32-bit limbs to 64-bit lumps and I think that *might* still work.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2020-05-05 19:27:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-624258676  

I've created a PR which integrates this, plus configuration/selection logic.  See https://github.com/boostorg/multiprecision/pull/233.

---

## Comment 16

> Username: madhur4127  
> Created_at: 2020-05-06 04:30:25 UTC  
> Updated_at: 2020-05-06 05:17:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-624435771  

```  
BM_addc_unroll_times_4<cpp_int>/1024       28235 ns        28250 ns        24889  
BM_addc_unroll_times_4<cpp_int>/2048       45913 ns        46039 ns        14933  
BM_addc_unroll_times_4<cpp_int>/4096       83938 ns        83702 ns         7467  
BM_addc_unroll_times_4<cpp_int>/8192      173034 ns       172631 ns         4073  
BM_addc_unroll_times_4<cpp_int>/16384     417814 ns       423825 ns         1659  
BM_addc_unroll_times_4<cpp_int>/32768     857495 ns       857597 ns          747  
BM_addc_unroll_times_4<cpp_int>/65536    1537910 ns      1535627 ns          407  
BM_adcx_unroll_times_4<cpp_int>/1024       23188 ns        22879 ns        28000  
BM_adcx_unroll_times_4<cpp_int>/2048       38956 ns        38504 ns        18667  
BM_adcx_unroll_times_4<cpp_int>/4096       69749 ns        69754 ns         8960  
BM_adcx_unroll_times_4<cpp_int>/8192      135489 ns       133929 ns         5600  
BM_adcx_unroll_times_4<cpp_int>/16384     293258 ns       291561 ns         2358  
BM_adcx_unroll_times_4<cpp_int>/32768     700856 ns       697545 ns         1120  
BM_adcx_unroll_times_4<cpp_int>/65536    1491634 ns      1474649 ns          498  
```  
  
There should have been no difference between ADC, ADCX versions at least in GCC because it doesn't even generate ADX (ADCX, AODX) instructions. Clang, on the other hand, supports ADCX but doesn't generate ADCX code in this case.  
  
In MSVC, ICC the only difference they made is to replace ADC by ADCX. I think this will hurt performance because even though both have a latency of 1 but the throughput of ADC is greater than ADCX.  
  
ADCX can be useful only when compiler recognizes use two addition chains:  
1. Loop counter `i`, using `ADOX`  
2. Adding big integers using `ADCX`  
  
For testing the end of loop: Using `lea` instead of `inc` and `jnz` instead of `jne` will not disturb CF (?).  
  
This is because many instructions change EFLAGS, especially CF & OF Flags. So compilers don't recognize it. GCC already accepted it as its limitation.  
  
Using ADX efficiently is only possible in case of asm.

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2020-05-20 19:02:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-631665171  

Just to say I haven't forgotten this - I have fast subtraction working too - also abstracted in a similar manner to this - unfortunately I got pulled down a rabbit hole of an unrelated bug I discovered during local testing :(

---

## Comment 18

> Username: madhur4127  
> Created_at: 2020-05-25 15:41:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-633627683  

> /usr/bin/ld: ../../../bin.v2/libs/multiprecision/test/test_cpp_int_import_export.test/gcc-9.3.0/debug/threading-multi/visibility-hidden/test_cpp_int_import_export.o: in function `boost::multiprecision::backends::add_unsigned_adc(unsigned long long const*, unsigned long long const*, unsigned long long*, unsigned long, unsigned long)`:  
/home/madhur/multiprecision/test/../../../boost/multiprecision/cpp_int/add.hpp:54: undefined reference to `_addcarry_u64(unsigned char, unsigned long long, unsigned long long, unsigned long long*)`  
  
Idk why `b2` fails with this, @jzmaddock I was talking about this error.

---

## Comment 19

> Username: jzmaddock  
> Created_at: 2020-05-26 15:48:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/231#issuecomment-634110955  

> Idk why b2 fails with this, @jzmaddock I was talking about this error.  
  
I think it's because you're taking the address of _addcarry_u64 and that might not be a "proper" function (it is for GCC, but probably not Intel or MSVC).

---
