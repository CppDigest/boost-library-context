# #666 - Is there a way to make uint128_t really 128 bit? [Open]

> Username: pps83  
> Created at: 2025-03-19 20:42:32 UTC  
> Updated at: 2026-02-06 11:27:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666  

Not sure if it was raised by others, but is there a way to actually make uint128_t to be 128 bit? For some reason, I have mental rejection from using boost::multiprecision::uint128_t / int128_t because it's not true 128 bit int.  
I just saw explanation why [boost::multiprecision::uint128_t sizeof is 24](https://stackoverflow.com/questions/41876253/boostmultiprecisionuint128-t-sizeof-is-24) and it might make sense for some huge numbers, but not sure if it makes sense for 128 bit. To me it feels like uint64_t that was only usable for 60 bits with some bits used to track what 16-bit num is in use, or, as if uint64_t was actually 80bit in size. Both aren't OK imo if there was no way to opt out from it. Also, instead of using some var, to keep track of what's set, obviously, it would be better to comply test the number itself (if it's meant for checking zero or not).  
IMO, it would be better if uint128_t/int128_t from boost at least generated optimal code for regular math operations. Even `__msvc_int128.hpp` is more than 2x faster in my tests for basic calculations.

---

## Comment 1

> Username: mborland  
> Created at: 2025-03-20 13:29:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-2740462826  

@ckormanyos and I were talking about this recently. We have an optimized u128 that we use in the backend of a different library. Should this go into multi precision or a separate library? I lean towards the latter since 128 bit ints are more broadly used than arbitrary precision arithmetic.

---

## Comment 2

> Username: pps83  
> Created at: 2025-03-20 16:47:45 UTC  
> Updated at: 2025-03-20 16:58:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-2741086426  

> We have an optimized u128 that we use in the backend of a different library  
  
are you referring to https://github.com/ckormanyos/wide-integer ? I looked into the code and decided to skip it. Only [intx](https://github.com/chfast/intx) seemed like good to test.   
  
As a side note, I compared multiple libs and it looks like [__msvc_int128.hpp](https://github.com/microsoft/STL/blob/main/stl/inc/__msvc_int128.hpp) is overall does really well compared to all others. The only case where it's loses is when built-in `__int128_t`/`__uint128_t` could be used (and it seems that clang has some issues supporting them on windows). [abseil-uint128_t](https://github.com/abseil/abseil-cpp/blob/master/absl/numeric/int128.h), for example, is extremely slow on ms compiler for some reason.  
  
> I lean towards the latter since 128 bit ints are more broadly used than arbitrary precision arithmetic.  
  
In my case, the only reason I touched [multiprecision](https://github.com/boostorg/multiprecision) was to get some 128bit int math done instead of wasting time doing it all myself. Apparently, it's not exactly what I think it was. Perhaps, most of the users also come to take simplest stuff like me: 128 int/uint

---

## Comment 3

> Username: mborland  
> Created at: 2025-03-20 17:28:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-2741190200  

> > We have an optimized u128 that we use in the backend of a different library  
>   
> are you referring to https://github.com/ckormanyos/wide-integer ? I looked into the code and decided to skip it. Only [intx](https://github.com/chfast/intx) seemed like good to test.  
>   
  
No, it's part of a decimal floating point library. This issue and linked PR can be found here: https://github.com/cppalliance/decimal/issues/885. I'll have to check against `__msvc_int128.hpp`.

---

## Comment 4

> Username: pps83  
> Created at: 2025-03-24 15:38:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-2748563000  

How would `mp::int128_t` convert to `mp::uint128_t` if I cast from one to the other? Would it do arithmetic shift magic to preserve sign bit?   
  
```c++  
void test(mp::int128_t n)  
{  
    mp::uint128_t u = (mp::uint128_t)n;  
    mp::int128_t i = (mp::int128_t)u;  
    assert(n == i);     
}  
```

---

## Comment 5

> Username: ckormanyos  
> Created at: 2025-06-28 19:11:50 UTC  
> Updated at: 2025-06-28 19:21:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3015989649  

Hi @pps83 I have noted that Matt (@mborland) has actually created [cppalliance/int128](https://github.com/cppalliance/int128).  
  
One of the purposes of this library (in addition to dependency-free raw speed) is to have a fixed-width 128-bit signed/unsigned integral representation.  
  
I do not believe that Boost.Multiprecision will evolve in the direction of your original query. But perhaps this potential new library (which does exactly what you queried) is what you seek?  
  
Cc: @mborland and @jzmaddock

---

## Comment 6

> Username: pps83  
> Created at: 2025-07-01 13:35:27 UTC  
> Updated at: 2025-07-01 13:57:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024056993  

From the description it does look like a good lib for that as it tries to do only this part.  
  
So far, lib-only code seems to be best [from msft](https://github.com/microsoft/STL/blob/main/stl/inc/__msvc_int128.hpp).  
  
For my own use I have this:  
```  
#pragma once  
#if defined(__SIZEOF_INT128__) && (!defined(_MSC_VER) || defined(__INTEL_LLVM_COMPILER))  
typedef __int128_t int128_t;  
typedef __uint128_t uint128_t;  
#elif defined(_MSC_VER)  
#include <__msvc_int128.hpp>  
typedef std::_Signed128 int128_t;  
typedef std::_Unsigned128 uint128_t;  
#else  
#include <boost/multiprecision/cpp_int.hpp>  
typedef boost::multiprecision::int128_t int128_t; // 196 bit  
typedef boost::multiprecision::uint128_t uint128_t; // 196 bit  
#endif  
```  
  
and then I do all kinds of 128bit math. In my tests/benchmarks, when I could use clang's built-in 128bit math vs using `__msvc_int128.hpp` with clang the results were that the ms code behaved identical or even better in some cases (unlike all other libraries that I tried).  
Do things like gcd/lcm work with it?

---

## Comment 7

> Username: ckormanyos  
> Created at: 2025-07-01 13:58:35 UTC  
> Updated at: 2025-07-01 13:58:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024144804  

Yes Multiprecision integral types have a small selection of number-theoretical functions including `gcd`, `lcm` and `miller_rabin`.

---

## Comment 8

> Username: mborland  
> Created at: 2025-07-01 14:05:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024187812  

> From the description it does look like a good lib for that as it tries to do only this part.  
>   
> So far, lib-only code seems to be best [from msft](https://github.com/microsoft/STL/blob/main/stl/inc/__msvc_int128.hpp).  
>   
> For my own use I have this:  
>   
> ```  
> #pragma once  
> #if defined(__SIZEOF_INT128__) && (!defined(_MSC_VER) || defined(__INTEL_LLVM_COMPILER))  
> typedef __int128_t int128_t;  
> typedef __uint128_t uint128_t;  
> #elif defined(_MSC_VER)  
> #include <__msvc_int128.hpp>  
> typedef std::_Signed128 int128_t;  
> typedef std::_Unsigned128 uint128_t;  
> #else  
> #include <boost/multiprecision/cpp_int.hpp>  
> typedef boost::multiprecision::int128_t int128_t; // 196 bit  
> typedef boost::multiprecision::uint128_t uint128_t; // 196 bit  
> #endif  
> ```  
>   
> and then I do all kinds of 128bit math. In my tests/benchmarks, when I could use clang's built-in 128bit math vs using `__msvc_int128.hpp` with clang the results were that the ms code behaved identical or even better in some cases (unlike all other libraries that I tried). Do things like gcd/lcm work with it?  
  
Can you share your tests/benchmarks?   
  
Relative to `__msvc_int128` the linked lib is at parity or better on MSVC x64 with basic operations:  
  
https://github.com/cppalliance/int128/blob/develop/doc/img/u128_graphs/windows/x64_relative_performance.png

---

## Comment 9

> Username: pps83  
> Created at: 2025-07-01 14:25:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024278090  

> Can you share your tests/benchmarks?  
  
I had a local change in my code for evaluation/test, didn't check it in and lost. I wanted to check different libs for different cases that I use in my code (gcd, mulhi (64 high bits of 64x64 multiplication), regular multiplication by 32, 64 bit, 128/64 division etc). I used msvc, clang-cl, clang, gcc on windows/linux in 32/64 bits. __msvc_int128 is only usable on windows (msvc/clang-cl) and it did surprisingly well compared to others (and even vs linux when I tested same hw with clang/gcc with their __int128 types on x64).  
I think I still have to use boost/mutiprecission because of gcd or 32bit builds. Perhaps, I can replace it all with [cppalliance/int128](https://github.com/cppalliance/int128).

---

## Comment 10

> Username: mborland  
> Created at: 2025-07-01 14:29:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024293237  

No worries. I opened [an issue](https://github.com/cppalliance/int128/issues/216) to add `gcd` and `lcm` to int128. I have to figure out why my graphs don't show up in the docs, but you can see them all [here](https://github.com/cppalliance/int128/tree/develop/doc/img). The relative is builtin, int128, and boost::multiprecision::(u)int128_t

---

## Comment 11

> Username: ckormanyos  
> Created at: 2025-07-01 15:37:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024554103  

You could use wide-integer temporarily and transition to Matt's library later. Matt's library will be a better fit in the long run since it is tuned for 128

---

## Comment 12

> Username: jzmaddock  
> Created at: 2025-07-01 15:43:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024576009  

Something I always left the door open for in mutiprecision, but never got around to, was to support something other than signed-magnitude in cpp_int - specifically 2's complement.  The latter would have the advantage of being "packed" so 128-bits for a 128-bit integer, but would lack the limb-count so loose efficiency for large sizes (but almost certainly gain a lot at smaller sizes).  Perhaps it's time to see if we can appropriate Matt's code for that use case...

---

## Comment 13

> Username: ckormanyos  
> Created at: 2025-07-01 15:56:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024617810  

Oh that's a good idea. Just integrate Matt's code for 128 into Multiprecision. I can help with that, tests and whatnot.

---

## Comment 14

> Username: ckormanyos  
> Created at: 2025-07-01 15:57:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024620126  

And we would need the eval_whatever functions.

---

## Comment 15

> Username: pps83  
> Created at: 2025-07-01 16:10:27 UTC  
> Updated at: 2025-08-24 12:17:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024667464  

I tested the part where I use [gcd](https://en.cppreference.com/w/cpp/numeric/gcd.html) and I'm floored that boost::mp actually performs best.  
  
Here's test code I used:  
```  
struct splitdata  
{  
    uint64_t from, from_m;  
    uint64_t to, to_m;  
};  
template<typename uint128_t>  
static void splits_test(std::vector<splitdata>& in_v, std::vector<splitdata>& out_v)  
{  
    out_v.resize(in_v.size());  
    for (size_t i = 0; i < in_v.size(); ++i)  
    {  
        auto& in = in_v[i];  
  
        uint128_t x = uint128_t(in.from) * in.to_m;  
        uint128_t y = uint128_t(in.to) * in.from_m;  
  
        uint128_t z = gcd(x, y);  
        auto from = uint64_t(x / z);  
        auto to = uint64_t(y / z);  
  
        auto& out = out_v[i];  
        out.from = from;  
        out.to = to;  
        //assert(nearly_equal(from, to, split_from, split_to));  
    }  
}  
```  
  
gcd code works for `boost::multiprecision::uint128_t` as-is:  
```  
std::vector<splitdata> parse_splits_data;  
std::vector<splitdata> results;  
splits_test<boost::multiprecision::uint128_t>(parse_splits_data, results);  
```  
When I tried to use  
```  
splits_test<std::_Unsigned128>(parse_splits_data, results);  
```  
it failed with all kinds of static asserts inside std because `std::_Unsigned128` cannot be used for std::gcd. I copied their impl and made it work, and outputs were identical to boost::mp (to verify math correctness). I did the same for boost::int128::uint128_t.  
  
Then I tried to benchmark the code. Here's the result:  
```  
     1.592ms  `splits_test<boost::multiprecision::uint128_t>(parse_splits_data, results_mp)`  
     6.562ms  `splits_test<std::_Unsigned128>(parse_splits_data, results_ms)`  
     7.788ms  `splits_test<boost::int128::uint128_t>(parse_splits_data, results_cp)`  
parse_splits_count:4549  
```  
  
I tried to investigate, and it appears that out of 4549 only 3 entries had to use 128bit math. I updated the code to conditionally use 128bit only when needed:  
```  
// inside of splits_test:  
...   
        if (x > UINT64_MAX || y > UINT64_MAX)  
        {  
            uint128_t z = gcd(x, y);  
            auto from = uint64_t(x / z);  
            auto to = uint64_t(y / z);  
  
            auto& out = out_v[i];  
            out.from = from;  
            out.to = to;  
        }  
        else  
        {  
            uint64_t lx = (uint64_t)x;  
            uint64_t ly = (uint64_t)y;  
  
            auto z = std::gcd(lx, ly);  
            auto from = lx / z;  
            auto to = ly / z;  
  
            auto& out = out_v[i];  
            out.from = from;  
            out.to = to;  
        }  
```  
and results were like this:  
  
  
![Image](https://github.com/user-attachments/assets/466c718d-c76c-4a3a-ae62-3249fe059a1c)  
  
That's 64bit build with latest ms compiler.

---

## Comment 16

> Username: ckormanyos  
> Created at: 2025-07-01 16:31:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024750049  

> I'm floored that boost::mp actually performs best  
  
I'm not. John and Matt had a huge effort in optimizing that. It spanned weeks a couple years back.  
  
I tried to _twiddle_ with it this year and lost performance. So I did not push that to develop. We have good `gcd`.

---

## Comment 17

> Username: pps83  
> Created at: 2025-07-01 16:45:05 UTC  
> Updated at: 2025-07-01 16:48:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024788031  

Sorry for false info, it appears that my entire project locally used asan. I disabled it, and retested with/without check for 128bit:  
```  
clang-cl (128bit check):  
     174.2us  `splits_test<boost::multiprecision::uint128_t>(parse_splits_data, results_mp)`  
     110.5us  `splits_test<std::_Unsigned128>(parse_splits_data, results_ms)`  
     108.0us  `splits_test<boost::int128::uint128_t>(parse_splits_data, results_cp)`  
  
     231.8us  `splits_test<boost::multiprecision::uint128_t>(parse_splits_data, results_mp)`  
     259.0us  `splits_test<std::_Unsigned128>(parse_splits_data, results_ms)`  
     270.8us  `splits_test<boost::int128::uint128_t>(parse_splits_data, results_cp)`  
  
msvc (128bit check):  
     202.5us  `splits_test<boost::multiprecision::uint128_t>(parse_splits_data, results_mp)`  
     120.4us  `splits_test<std::_Unsigned128>(parse_splits_data, results_ms)`  
     125.9us  `splits_test<boost::int128::uint128_t>(parse_splits_data, results_cp)`  
	   
     264.7us  `splits_test<boost::multiprecision::uint128_t>(parse_splits_data, results_mp)`  
     388.0us  `splits_test<std::_Unsigned128>(parse_splits_data, results_ms)`  
     0.682ms  `splits_test<boost::int128::uint128_t>(parse_splits_data, results_cp)`  
```  
  
as usual msvc is 10-20% slower than clang-cl (it used to be much better in 32bit builds in general).  
I think `boost::mp` is better without the 128-bit check because internally it does checks if math can be done with 64bit only (or even 32bit only).  
  
From the results above, here's concerning part: msvc without 128bit requirement check:  
```  
     388.0us  `splits_test<std::_Unsigned128>(parse_splits_data, results_ms)`  
     0.682ms  `splits_test<boost::int128::uint128_t>(parse_splits_data, results_cp)`  
```  
  
boost::int128 is twice as slow, while with clang-cl it has the same perf. I don't think it's due to gcd code, as I used identical code for both of them for my local testing (as explained above). Has to be due to this part:  
```  
            auto from = lx / z;  
            auto to = ly / z;  
```  
maybe it's code issue, maybe `_Unsigned128` internally checks if 64bit math can be used, while boost::int128 doesn't check

---

## Comment 18

> Username: pps83  
> Created at: 2025-07-01 16:50:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024801395  

I copied this code for [gcd from ms stl](https://github.com/microsoft/STL/blob/313964b78a8fd5a52e7965e13781f735bcce13c5/stl/inc/numeric#L661):  
  
```  
namespace boost  
{  
namespace int128  
{  
inline int _Countr_zero_tzcnt(const uint128_t _Val) noexcept  
{  
    return _Val.low ? std::_Countr_zero_tzcnt(_Val.low) : 64 + std::_Countr_zero_tzcnt(_Val.high);  
}  
inline constexpr std::common_type_t<uint128_t, uint128_t> gcd(const uint128_t _Mx, const uint128_t _Nx) noexcept /* strengthened */  
{  
    using namespace std;  
    using _Common = uint128_t;  
    using _Common_unsigned = uint128_t;  
  
    _Common_unsigned _Mx_magnitude = (_Mx);  
    _Common_unsigned _Nx_magnitude = (_Nx);  
    if (_Mx_magnitude == 0U) {  
        return static_cast<_Common>(_Nx_magnitude);  
    }  
  
    if (_Nx_magnitude == 0U) {  
        return static_cast<_Common>(_Mx_magnitude);  
    }  
  
    const auto _Mx_trailing_zeroes = static_cast<unsigned long>(_Countr_zero_tzcnt(_Mx_magnitude));  
    auto _Nx_trailing_zeroes = static_cast<unsigned long>(_Countr_zero_tzcnt(_Nx_magnitude));  
    const auto _Common_factors_of_2 = (_STD min)(_Mx_trailing_zeroes, _Nx_trailing_zeroes);  
    _Mx_magnitude >>= _Mx_trailing_zeroes;  
    for (;;) {  
        _Nx_magnitude >>= _Nx_trailing_zeroes;  
        if (_Mx_magnitude > _Nx_magnitude) {  
            _Common_unsigned _Temp = _Mx_magnitude;  
            _Mx_magnitude = _Nx_magnitude;  
            _Nx_magnitude = _Temp;  
        }  
  
        _Nx_magnitude -= _Mx_magnitude;  
        if (_Nx_magnitude == 0U) {  
            return static_cast<_Common>(_Mx_magnitude << _Common_factors_of_2);  
        }  
  
        _Nx_trailing_zeroes = static_cast<unsigned long>(_Countr_zero_tzcnt(_Nx_magnitude));  
    }  
}  
}  
}  
```

---

## Comment 19

> Username: ckormanyos  
> Created at: 2025-07-01 16:52:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024805736  

MSVC will use 32-bit limbs, while clang might use 64 bit limbs. It is hard to diagnose exactly your case from a disatcnce, but the limb size might be the determining factor?

---

## Comment 20

> Username: mborland  
> Created at: 2025-07-01 17:05:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024864585  

> MSVC will use 32-bit limbs, while clang might use 64 bit limbs. It is hard to diagnose exactly your case from a disatcnce, but the limb size might be the determining factor?  
  
MSVC uses a bunch of their own intrinsics in the implementation. The problem is many of those intrinsics have measurably worse performance than typical scalar code which is what Clang-CL uses.

---

## Comment 21

> Username: pps83  
> Created at: 2025-07-01 17:13:45 UTC  
> Updated at: 2025-07-01 17:14:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024890048  

> The problem is many of those intrinsics have measurably worse performance than typical scalar code which is what Clang-CL uses.  
  
clang-cl code uses the same __msvc_int128.hpp code as msvc for the `results_ms` part. You were referring to what clang-cl uses for `boost::int128` (`results_cp` part)?

---

## Comment 22

> Username: mborland  
> Created at: 2025-07-01 17:22:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024915058  

>   
> clang-cl code uses the same __msvc_int128.hpp code as msvc for the `results_ms` part. You were referring to what clang-cl uses for `boost::int128` (`results_cp` part)?  
  
I meant for the basic operators (+, -, *, /) I believe Clang-CL doesn't take the intrinsics path like this example: https://github.com/microsoft/STL/blob/main/stl/inc/__msvc_int128.hpp#L127. There were a few of the intrinsics that I tried, but ended up dropping due to bad codegen.

---

## Comment 23

> Username: ckormanyos  
> Created at: 2025-07-01 17:25:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3024921093  

Ah OK. Got it.

---

## Comment 24

> Username: mborland  
> Created at: 2025-07-03 13:26:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3032265971  

I merged specializations for GCD and LCM into https://github.com/cppalliance/int128

---

## Comment 25

> Username: ckormanyos  
> Created at: 2025-07-21 19:03:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3098025365  

> Is there a way to make uint128_t really 128 bit?  
  
Yes @pps83, you can use Matt's new `(u)int128_t` standalone repository.  
  
I would like to begin dialog with @jzmaddock and @mborland regarding this thing. Could/Should we simply import this into Multiprecision in an upcoming release cycle down the road?

---

## Comment 26

> Username: mborland  
> Created at: 2025-07-21 19:12:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3098049061  

So the original intent was to merge it into Boost.Core since it's broadly useful, but the final size exceeds what Peter is comfortable with (it would represent about ~1/3 of the entire Boost.Core LOCs). I think it's better as a standalone lib than in  here. At least decimal and charconv would use it and I'd rather add 1 dependency than the 25 that come with Multiprecision: https://pdimov.github.io/boostdep-report/develop/module-weights.html

---

## Comment 27

> Username: ckormanyos  
> Created at: 2025-07-21 19:22:11 UTC  
> Updated at: 2025-07-21 19:23:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3098076062  

> So the original intent was to merge it into Boost.Core since it's broadly useful, but the final size exceeds what Peter is comfortable with   
  
That's a good point with the many, many lines of code and test code.  
  
I guess we could open it up, and go for inclusion as a separate (and new) Boost LIB. That's another idea. We could roll 128 and 256 together and go the _hard_ _path_ to full inclusion as a separate Boost LIB?  
  
We need the review, the whole jazz for that. But could be done.

---

## Comment 28

> Username: mborland  
> Created at: 2025-07-21 19:26:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3098100967  

I think the hard path may be the best one. I haven't really posted too much about the lib since I want to keep hammering it in Decimal, but I'm nearly certain it works as intended across platforms.

---

## Comment 29

> Username: ckormanyos  
> Created at: 2025-07-21 19:31:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3098132058  

> I think the hard path may be the best one. I haven't really posted too much about the lib since I want to keep hammering it in Decimal, but I'm nearly certain it works as intended across platforms.  
  
I agree.  
  
The road will be challenging, but I am willing to assist where I can. I'm now convinced that it is best to go for a separate LIB for these useful data types. These will also be mandatory if we ever get around to writing `float128_t` in a generic way.

---

## Comment 30

> Username: pps83  
> Created at: 2025-08-26 09:09:43 UTC  
> Updated at: 2025-08-26 11:35:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3223299284  

I retested again and the lib looks quite good now. Any plans to merge it to boost v1.90?  
In my tests it performs better than multiprecision::uint128_t and better than __uint128_t. Only with with msvc their `std::_Unsigned128` is faster:  
  
```  
[ RUN      ] Int128.SplitsTest1  
  
|               ns/op |                op/s |    err% |     total | benchmark  
|--------------------:|--------------------:|--------:|----------:|:----------  
|          834,300.00 |            1,198.61 |    1.3% |      0.01 | `splits_test<boost::int128::uint128_t>(in, res_int128)`  
|          908,300.00 |            1,100.96 |    0.9% |      0.01 | `splits_test<boost::multiprecision::uint128_t>(in, res_mp)`  
|          678,900.00 |            1,472.97 |    3.1% |      0.01 | `splits_test<std::_Unsigned128>(in, res_ms)`  
[       OK ] Int128.SplitsTest1 (34 ms)  
[----------] 1 test from Int128 (34 ms total)  
```  
  
  
I used this code for simple tests:  
  
```  
#define ANKERL_NANOBENCH_IMPLEMENT  
#include "gtest/gtest.h"  
#include <nanobench.h> // https://raw.githubusercontent.com/martinus/nanobench/refs/heads/master/src/include/nanobench.h  
#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/int128.hpp>  
#if defined(_MSC_VER)  
#include <__msvc_int128.hpp>  
#endif  
#include <array>  
#include <stdint.h>  
#include <string.h>  
  
#if defined(__GNUC__) || defined(__clang__)  
#define NOINLINE __attribute__((__noinline__))  
#elif defined(_MSC_VER)  
#define NOINLINE __declspec(noinline)  
#endif  
  
struct splitdata  
{  
    uint64_t from, from_m;  
    uint64_t to, to_m;  
};  
  
static bool operator==(const splitdata& lhs, const splitdata& rhs)  
{  
    return 0 == memcmp(&lhs, &rhs, sizeof(rhs));  
}  
  
namespace  
{  
#ifdef _MSC_VER  
inline int countr_zero(const std::_Unsigned128 _Val) noexcept  
{  
    return _Val._Word[0] ? std::_Countr_zero_tzcnt(_Val._Word[0]) : 64 + std::_Countr_zero_tzcnt(_Val._Word[1]);  
}  
#elif defined(__x86_64__) || defined(__aarch64__)  
inline int countr_zero(const __uint128_t _Val) noexcept  
{  
    return static_cast<uint64_t>(_Val) ? __builtin_ctzll(static_cast<uint64_t>(_Val)) : 64 + __builtin_ctzll(static_cast<uint64_t>(_Val >> 64));  
}  
#endif  
template<typename U128>  
inline constexpr U128 gcd(const U128 _Mx, const U128 _Nx) noexcept /* strengthened */  
{  
    using _Common = U128;  
    using _Common_unsigned = U128;  
  
    _Common_unsigned _Mx_magnitude = (_Mx);  
    _Common_unsigned _Nx_magnitude = (_Nx);  
    if (_Mx_magnitude == 0U)  
        return static_cast<_Common>(_Nx_magnitude);  
    if (_Nx_magnitude == 0U)  
        return static_cast<_Common>(_Mx_magnitude);  
  
    const auto _Mx_trailing_zeroes = static_cast<unsigned long>(countr_zero(_Mx_magnitude));  
    auto _Nx_trailing_zeroes = static_cast<unsigned long>(countr_zero(_Nx_magnitude));  
    const auto _Common_factors_of_2 = (std::min)(_Mx_trailing_zeroes, _Nx_trailing_zeroes);  
    _Mx_magnitude >>= _Mx_trailing_zeroes;  
    for (;;)  
    {  
        _Nx_magnitude >>= _Nx_trailing_zeroes;  
        if (_Mx_magnitude > _Nx_magnitude)  
        {  
            _Common_unsigned _Temp = _Mx_magnitude;  
            _Mx_magnitude = _Nx_magnitude;  
            _Nx_magnitude = _Temp;  
        }  
        _Nx_magnitude -= _Mx_magnitude;  
        if (_Nx_magnitude == 0U)  
            return static_cast<_Common>(_Mx_magnitude << _Common_factors_of_2);  
        _Nx_trailing_zeroes = static_cast<unsigned long>(countr_zero(_Nx_magnitude));  
    }  
}  
}  
  
template<typename u128>  
static NOINLINE void splits_test(const std::vector<splitdata>& in_v, std::vector<splitdata>& out_v)  
{  
    for (size_t i = 0; i < in_v.size(); ++i)  
    {  
        auto& in = in_v[i];  
  
        u128 x = u128(in.from) * in.to_m;  
        u128 y = u128(in.to) * in.from_m;  
  
        u128 z = gcd(x, y);  
        auto from = uint64_t(x / z);  
        auto to = uint64_t(y / z);  
  
        auto& out = out_v[i];  
        out.from = from;  
        out.to = to;  
        //assert(nearly_equal(from, to, split_from, split_to));  
    }  
}  
  
#define NANOBENCH(...) ankerl::nanobench::Bench().run(#__VA_ARGS__, [&]() { __VA_ARGS__; })  
  
TEST(Int128, SplitsTest1)  
{  
    uint32_t n = 12345;  
    std::vector<splitdata> in;  
    for (int i = 0; i < 4; ++i)  
    {  
        splitdata sd = {1, 1, 1, 1};  
        (&sd.from)[i] = n;  
        for (int i = 0; i < 10000; ++i)  
            in.push_back(sd);  
    }  
  
    std::vector<splitdata> res_mp, res_ms, res_cl, res_int128;  
    res_mp.resize(in.size());  
    res_ms.resize(in.size());  
    res_cl.resize(in.size());  
    res_int128.resize(in.size());  
  
    NANOBENCH(splits_test<boost::int128::uint128_t>(in, res_int128));  
  
    NANOBENCH(splits_test<boost::multiprecision::uint128_t>(in, res_mp));  
    ASSERT_EQ(res_mp, res_int128);  
#ifdef _MSC_VER  
    NANOBENCH(splits_test<std::_Unsigned128>(in, res_ms));  
    ASSERT_EQ(res_mp, res_ms);  
#endif  
#if (defined(__INTEL_LLVM_COMPILER) || defined(__GNUC__)) && (defined(_M_X64) || defined(__x86_64__) || defined(_M_ARM64) || defined(__aarch64__))  
    NANOBENCH(splits_test<__uint128_t>(in, res_cl));  
    ASSERT_EQ(res_mp, res_cl);  
#endif  
}  
```

---

## Comment 31

> Username: mborland  
> Created at: 2025-08-26 09:30:35 UTC  
> Updated at: 2025-08-26 09:30:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3223368184  

Hopefully it'll be in for 1.91. Chris and I soon have the re-review of the decimal floating point library (https://github.com/cppalliance/decimal) with John managing the review. Once that library is settled we will try and get int128 into boost independent of Boost.Multiprecision

---

## Comment 32

> Username: pps83  
> Created at: 2025-08-26 11:45:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3223835932  

for refs I used this code (pasted above).  
https://godbolt.org/z/bbsjjxYEM  
  
with godbolt  
```  
#include <https://github.com/cppalliance/int128/blob/develop/include/boost/int128.hpp>  
```  
cannot be included though, needs an amalgamated header

---

## Comment 33

> Username: ckormanyos  
> Created at: 2026-02-06 11:27:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/666#issuecomment-3859895913  

> Hopefully it'll be in for 1.91. Chris and I soon have the re-review of the decimal floating point library  
  
I think 1.91 is a bit optimistic still. The re-review of Decimal took place with positive result and Decimal is planned for 1.91.  
  
Hi @mborland what are your current thoughts regarding [cppalliance/int128](https://github.com/cppalliance/int128)?
