# #585 - cpuid / sse42 broken on gcc/clang [Closed]

> Username: vinniefalco  
> Created at: 2017-07-04 01:50:14 UTC  
> Updated at: 2017-07-09 03:46:31 UTC  
> Closed at: 2017-07-09 03:46:31 UTC  
> Url: https://github.com/boostorg/beast/issues/585  

Seems it only works on Windows I need help getting it functioning elsewhere.

---

## Comment 1

> Username: mika-fischer  
> Created at: 2017-07-04 07:06:36 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312797882  

What exactly is broken?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-04 07:09:48 UTC  
> Updated at: 2017-07-04 07:10:06 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312798439  

It compiles but I dont think I got the compiler flags set to allow the intrinsics to be used. And I dont think the macros for detecting if intrinsics are available on gcc/clang are right. This all works on Windows by the way, 20% speedup. Here it is:  
https://github.com/vinniefalco/Beast/blob/v72/include/beast/core/detail/cpu_info.hpp#L11  
  
Thanks for looking!

---

## Comment 3

> Username: mika-fischer  
> Created at: 2017-07-04 07:43:07 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312804972  

1) You need `-march=<something that has sse4.2>` or `-msse42` directly to make gcc provide these intrinsics. Probably the same with clang. MSVC doesn't have or need this.  
2) You should `#include <x86intrin.h>` (GCC, clang) or `#include <intrin.h>` (MSVC) to provide the prototypes.  
3) The detection is wrong, try this  
```  
# if defined(__SSE4_2__) || defined(_MSC_VER)  
#  define BEAST_NO_INTRINSICS 0  
# else  
#  define BEAST_NO_INTRINSICS 1  
# endif  
```  
4) I find the double negation confusing, why not BEAST_USE_INTRINISCS?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-07-04 07:50:42 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312806582  

Could I trouble you to submit a pull request? Then Travis will kick in and we'll see if it works  
  
>I find the double negation confusing, why not BEAST_USE_INTRINISCS?  
That's the Boost style

---

## Comment 5

> Username: octopus-prime  
> Created at: 2017-07-04 08:00:56 UTC  
> Updated at: 2017-07-04 10:46:30 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312808781  

Don't use the __asm stuff! You can use this for gcc and clang:  
  
```  
#include <iostream>  
#include <cstdlib>  
#include <cpuid.h>  
  
int main()  
{  
    std::uint32_t id = 0x1;  
    std::uint32_t eax = 0;  
    std::uint32_t ebx = 0;  
    std::uint32_t ecx = 0;  
    std::uint32_t edx = 0;  
  
    bool supported = __get_cpuid(id,  &eax,  &ebx,  &ecx, &edx);  
  
    std::cout << std::boolalpha << supported << std::endl;  
    std::cout << eax << std::endl;  
    std::cout << ebx << std::endl;  
    std::cout << ecx << std::endl;  
    std::cout << edx << std::endl;  
  
    return 0;  
}  
```  
  
Tested with  
- Apple LLVM version 7.3.0 (clang-703.0.31)  
- gcc 4.8.1 (wandbox)

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-04 08:02:22 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312809100  

-> https://github.com/vinniefalco/Beast/pulls

---

## Comment 7

> Username: mika-fischer  
> Created at: 2017-07-04 08:03:06 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312809251  

I realistically won't have time in the near future, sorry... Next week at the earliest. Maybe @octopus-prime wants to have a go at it?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-07-04 08:03:27 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312809337  

There's no rush at all

---

## Comment 9

> Username: octopus-prime  
> Created at: 2017-07-04 08:04:18 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312809551  

```  
/* Return cpuid data for requested cpuid level, as found in returned  
   eax, ebx, ecx and edx registers.  The function checks if cpuid is  
   supported and returns 1 for valid cpuid information or 0 for  
   unsupported cpuid level.  All pointers are required to be non-null.  */  
int __get_cpuid (unsigned int __level,  
    unsigned int *__eax, unsigned int *__ebx,  
    unsigned int *__ecx, unsigned int *__edx)  
  
```

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-07-04 08:06:10 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312809955  

I believe you... I think you should submit a pull request against **v72**! Maybe run the benchmarks before and after to see if its better?

---

## Comment 11

> Username: mika-fischer  
> Created at: 2017-07-04 08:10:09 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312810826  

@octopus-prime With GCC 4.8 and clang 3.7.1 there's even `__builtin_cpu_supports("sse4.2")` :-)

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-07-04 08:18:48 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312812780  

dang, that's awesome

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-07-04 08:19:19 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312812894  

20% speed up is waiting for you if you put in the work :)

---

## Comment 14

> Username: octopus-prime  
> Created at: 2017-07-04 08:58:26 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312822303  

@mika-fischer `__builtin_cpu_supports("sse4.2")` is runtime...  
  
Here is an example:  
  
```  
          if (__builtin_cpu_supports ("popcnt"))  
            {  
               asm("popcnt %1,%0" : "=r"(count) : "rm"(n) : "cc");  
            }  
          else  
            {  
               count = generic_countbits (n); //generic implementation.  
            }  
```  
  
If there is a dependency for SSE4.2 (i am not sure about that for `cpuid`) we should sort them out at compile time using something like:  
  
```  
#include <boost/predef/hardware/simd.h>  
  
#if (BOOST_HW_SIMD_X86 >= BOOST_HW_SIMD_X86_SSE4_2_VERSION)  
    ...  
#else  
    ...  
#endif  
```

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-07-04 09:01:41 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312823071  

The way I wrote the code is so that the instructions can be detected at run-time, this way the same binary will work on i386 systems with or without SSE4.2. If the extensions are available, it will use them, otherwise it will just run the regular code. There are two settings here: 1. Whether the extensions are available at compile time (needs a switch I guess) and 2. Whether the extensions are available at run-time (call __cpuid). Beast caches the value at run-time in the cpu_info.

---

## Comment 16

> Username: mika-fischer  
> Created at: 2017-07-04 09:01:50 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312823110  

@octopus-prime cpuid is also runtime. That's the point. That way the executable still works on processors without SSE4.2

---

## Comment 17

> Username: octopus-prime  
> Created at: 2017-07-04 09:13:20 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312826731  

Okay, got it now :-)

---

## Comment 18

> Username: octopus-prime  
> Created at: 2017-07-04 09:49:59 UTC  
> Updated at: 2017-07-04 10:45:47 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312835473  

```  
#include <iostream>  
#include <cstdlib>  
#include <cpuid.h>  
#include <chrono>  
  
bool check_sse42_by_cpuid()  
{  
    std::uint32_t id = 0x1;  
    std::uint32_t eax = 0;  
    std::uint32_t ebx = 0;  
    std::uint32_t ecx = 0;  
    std::uint32_t edx = 0;  
  
    __get_cpuid(id,  &eax,  &ebx,  &ecx, &edx);  
  
    return ecx & 1 << 20;  
}  
  
bool check_sse42_by_builtin_cpu_supports()  
{  
    return __builtin_cpu_supports("sse4.2");  
}  
  
template <typename S, typename F>  
void test(S string, F check)  
{  
    using namespace std::chrono;  
    auto const t0 = high_resolution_clock::now();  
    auto const r = check();  
    auto const t1 = high_resolution_clock::now();  
  
    std::cout << string << ": " << std::boolalpha << r << ", " << duration_cast<nanoseconds>(t1 - t0).count() << "ns" << std::endl;  
}  
  
int main()  
{  
    test("by_cpuid", check_sse42_by_cpuid);  
    test("by_builtin_cpu_supports", check_sse42_by_builtin_cpu_supports);  
    return 0;  
}  
```  
  
==>  
  
```  
by_cpuid: true, 3991ns  
by_builtin_cpu_supports: true, 216ns  
```  
  
So the good news is `builtin_cpu_supports` is at least 10x faster than `cpuid`.  
But the bad news is for clang (at least on osx using libc++) the code does compile but not link.

---

## Comment 19

> Username: mika-fischer  
> Created at: 2017-07-04 09:53:29 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312836325  

I don't think speed matters too much, since this will be only executed once per process. Also note that the check with cpuid is wrong, see https://github.com/vinniefalco/Beast/blob/v72/include/beast/core/detail/cpu_info.hpp#L87-L92

---

## Comment 20

> Username: octopus-prime  
> Created at: 2017-07-04 09:55:01 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312836655  

BTW: is this correct??  
`sse42 = (ecx & 20) != 0;`  
  
https://en.wikipedia.org/wiki/CPUID  
says for SSE4.2 bit 20 on ecx  
  
So it should be  
`sse42 = (ecx & 1 << 20) != 0;`

---

## Comment 21

> Username: mika-fischer  
> Created at: 2017-07-04 09:57:03 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312837081  

Good catch! :-)

---

## Comment 22

> Username: octopus-prime  
> Created at: 2017-07-04 10:57:18 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312848985  

> So the good news is `builtin_cpu_supports` is at least 10x faster than `cpuid`.  
  
I think this is not true. Maybe wandbox runs on virtualised hardware...  
On native hardware i get this:  
  
`by_cpuid: true, 169ns`

---

## Comment 23

> Username: vinniefalco  
> Created at: 2017-07-04 13:50:45 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312883320  

**WOW** so you're telling me I got 20% and that's **without** SSE4.2?!

---

## Comment 24

> Username: mika-fischer  
> Created at: 2017-07-04 13:56:39 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312884615  

Probably the broken check also returned true (by chance) and so SSE4.2 got used after all.

---

## Comment 25

> Username: vinniefalco  
> Created at: 2017-07-04 13:59:32 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312885278  

Oh :(

---

## Comment 26

> Username: octopus-prime  
> Created at: 2017-07-04 18:47:26 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312934103  

Are these the corresponding benchmarks?  
  
- beast.benchmarks.buffers  
- beast.benchmarks.parser

---

## Comment 27

> Username: vinniefalco  
> Created at: 2017-07-04 19:10:05 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312936432  

Yes, you can open buffers.cpp and just comment out the line   
```  
BEAST_DEFINE_TESTSUITE(buffers,benchmarks,beast);  
```  
To make the tests run faster (buffers doesn't benefit from intrinsics)

---

## Comment 28

> Username: octopus-prime  
> Created at: 2017-07-04 19:19:57 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312937475  

Some results here (Linux, gcc-7):  
  
587ms / 668ms = 88%  ==> -12% execution time  
  
Without `_mm_cmpestri`  
```  
beast.benchmarks.buffers  
  
count=1024, size=1024            prepare      with hint         random  
multi_buffer            :     12120 MB/s     11901 MB/s     11309 MB/s   63ms  
flat_buffer             :      6945 MB/s      6442 MB/s      5331 MB/s   121ms  
boost::asio::streambuf  :      6790 MB/s      7642 MB/s      5480 MB/s   115ms  
  
count=512, size=4096             prepare      with hint         random  
multi_buffer            :     24059 MB/s     23551 MB/s     21762 MB/s   64ms  
flat_buffer             :      9961 MB/s      9970 MB/s      7575 MB/s   166ms  
boost::asio::streambuf  :      8902 MB/s      8592 MB/s      6480 MB/s   191ms  
  
count=256, size=32768            prepare      with hint         random  
multi_buffer            :     28133 MB/s     28111 MB/s     27259 MB/s   215ms  
flat_buffer             :      8054 MB/s      7910 MB/s      6892 MB/s   792ms  
boost::asio::streambuf  :      7085 MB/s      7079 MB/s      5472 MB/s   929ms  
  
beast.benchmarks.parser  
beast.benchmarks.parser Parser speed test, 342764KB in 1000000 messages  
sizeof(request parser)  == 56  
sizeof(response parser) == 56  
http::basic_parser  
Trial 1: 668 ms  
Trial 2: 668 ms  
Trial 3: 668 ms  
Trial 4: 668 ms  
Trial 5: 668 ms  
Trial 6: 669 ms  
Trial 7: 668 ms  
Trial 8: 668 ms  
Trial 9: 668 ms  
Trial 10: 668 ms  
Longest suite times:  
    8.2s beast.benchmarks.buffers  
    6.7s beast.benchmarks.parser  
14.9s, 2 suites, 3 cases, 10002003 tests total, 0 failures  
```  
  
With `_mm_cmpestri`  
  
```  
beast.benchmarks.buffers  
  
count=1024, size=1024            prepare      with hint         random  
multi_buffer            :     12189 MB/s     11913 MB/s     11245 MB/s   63ms  
flat_buffer             :      6916 MB/s      6410 MB/s      5313 MB/s   122ms  
boost::asio::streambuf  :      8003 MB/s      8233 MB/s      5754 MB/s   105ms  
  
count=512, size=4096             prepare      with hint         random  
multi_buffer            :     25245 MB/s     25024 MB/s     22533 MB/s   61ms  
flat_buffer             :     10433 MB/s     10461 MB/s      7826 MB/s   159ms  
boost::asio::streambuf  :      9317 MB/s      8882 MB/s      6767 MB/s   183ms  
  
count=256, size=32768            prepare      with hint         random  
multi_buffer            :     29570 MB/s     29394 MB/s     27949 MB/s   207ms  
flat_buffer             :      8429 MB/s      8422 MB/s      6873 MB/s   766ms  
boost::asio::streambuf  :      7381 MB/s      7408 MB/s      5628 MB/s   895ms  
  
beast.benchmarks.parser  
beast.benchmarks.parser Parser speed test, 342764KB in 1000000 messages  
sizeof(request parser)  == 56  
sizeof(response parser) == 56  
http::basic_parser  
Trial 1: 586 ms  
Trial 2: 587 ms  
Trial 3: 587 ms  
Trial 4: 587 ms  
Trial 5: 587 ms  
Trial 6: 587 ms  
Trial 7: 587 ms  
Trial 8: 587 ms  
Trial 9: 587 ms  
Trial 10: 587 ms  
Longest suite times:  
    7.9s beast.benchmarks.buffers  
    5.9s beast.benchmarks.parser  
13.7s, 2 suites, 3 cases, 10002003 tests total, 0 failures  
```

---

## Comment 29

> Username: vinniefalco  
> Created at: 2017-07-04 19:20:38 UTC  
> Updated at: 2017-07-04 19:21:06 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312937546  

Not bad at all!!! Note that the speedup will be more noticeable with real-world / longer headers, since the intrinsics process 16 bytes at a time.

---

## Comment 30

> Username: octopus-prime  
> Created at: 2017-07-04 19:36:03 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312939105  

What are the supported compilers?  
  
- msvc  
- gcc  
- clang  
  
More?

---

## Comment 31

> Username: vinniefalco  
> Created at: 2017-07-04 19:38:24 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312939348  

For now msvc, gcc, clang, but when Beast becomes part of Boost then it will go into the test matrix and I will try to support as many compiler as is reasonably possible without creating a ton of work.

---

## Comment 32

> Username: octopus-prime  
> Created at: 2017-07-04 19:52:38 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312940816  

As @mika-fischer mentioned: There could be something wrong with `BEAST_NO_INTRINSICS`.  
Can we remove it for now? The supported compilers don't need.  
  
I would suggest for cpu_info.hpp  
  
```  
#if defined(_MSC_VER)  
#include <intrin.h> // __cpuid  
#else  
#include <cpuid.h>  // __get_cpuid  
#endif  
  
namespace beast {  
namespace detail {  
  
/*  Portions from Boost,  
    Copyright Andrey Semashev 2007 - 2015.  
*/  
template<class = void>  
void  
cpuid(  
    std::uint32_t id,  
    std::uint32_t& eax,  
    std::uint32_t& ebx,  
    std::uint32_t& ecx,  
    std::uint32_t& edx)  
{  
#if defined(_MSC_VER)  
    int regs[4];  
    __cpuid(regs, id);  
    eax = regs[0];  
    ebx = regs[1];  
    ecx = regs[2];  
    edx = regs[3];  
#else  
    __get_cpuid(id,  &eax,  &ebx,  &ecx, &edx);  
#endif  
}  
```  
  
And for basic_parser.hpp  
  
```  
#include <boost/predef/hardware/simd.h>  
  
#if (BOOST_HW_SIMD_X86 >= BOOST_HW_SIMD_X86_SSE4_2_VERSION)  
#include <nmmintrin.h>  
#endif  
  
...  
  
#if (BOOST_HW_SIMD_X86 >= BOOST_HW_SIMD_X86_SSE4_2_VERSION)  
  
    std::pair<char const*, bool>  
    find_fast(  
        char const* buf,  
        char const* buf_end,  
        char const* ranges,  
        size_t ranges_size)  
    {  
        bool found = false;  
  
        if(BOOST_LIKELY(sse42_))  
        {  
            if(BOOST_LIKELY(buf_end - buf >= 16))  
            {  
                __m128i ranges16 = _mm_loadu_si128((__m128i const*)ranges);  
                std::size_t left = (buf_end - buf) & ~15;  
                do  
                {  
                    __m128i b16 = _mm_loadu_si128((__m128i const*)buf);  
                    int r = _mm_cmpestri(ranges16, ranges_size, b16, 16,  
                        _SIDD_LEAST_SIGNIFICANT | _SIDD_CMP_RANGES | _SIDD_UBYTE_OPS);  
                    if(BOOST_UNLIKELY(r != 16))  
                    {  
                        buf += r;  
                        found = true;  
                        break;  
                    }  
                    buf += 16;  
                    left -= 16;  
                }  
                while(BOOST_LIKELY(left != 0));  
            }  
        }  
        return {buf, found};  
    }  
  
#else  
  
    constexpr std::pair<char const*, bool>  
    find_fast(  
        char const* buf,  
        char const* buf_end,  
        char const* ranges,  
        size_t ranges_size)  
    {  
    	return {buf, false};  
    }  
  
#endif  
```

---

## Comment 33

> Username: vinniefalco  
> Created at: 2017-07-04 20:01:44 UTC  
> Updated at: 2017-07-04 20:05:31 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312943941  

>here could be something wrong with BEAST_NO_INTRINSICS. Can we remove it for now? The supported compilers don't need.  
  
Hmm, no we need this macro but I don't mind if you change how it is set. There are some requirements:  
  
* `<beast/core/detail/cpu_info.hpp>`  checks and maybe sets `BEAST_NO_INTRINSICS`  
  
* If the macro is already set, then use the value  
  
* If that macro is not set, then the first thing that cpu_info.hpp should do is set the macro based on the availability of intrinsics, look at the preprocessor directives for this (`BOOST_HW_SIMD_X86 >= BOOST_HW_SIMD_X86_SSE4_2_VERSION`) for example.  
  
* Everywhere there is code which might use intrinsics, check the macro, i.e. `#if ! BEAST_NO_INTRINSICS`.  
  
Here's an example, probably wrong but you get the idea:  
```  
#if ! defined(BEAST_NO_INTRINSICS)  
#include <boost/predef/hardware/simd.h>  
# if (BOOST_HW_SIMD_X86 >= BOOST_HW_SIMD_X86_SSE4_2_VERSION)  
#  define BEAST_NO_INTRINSICS 0  
# else  
#  define BEAST_NO_INTRINSICS 1  
# endif  
#endif  
```  
  
Users need to be able to disable intrinsics if they want, by adding `-DBEAST_NO_INTRINSICS=1` to their compile flags.

---

## Comment 34

> Username: vinniefalco  
> Created at: 2017-07-04 21:11:27 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-312955148  

Please open a pull request :)

---

## Comment 35

> Username: octopus-prime  
> Created at: 2017-07-06 19:29:41 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-313495704  

Tried v73. SSE4.2 optimization was **not** enabled (here on Linux).  
I am building with -march=native and CPU has SSE4.2.  
  
So can we try another easy variant? Look:  
  
```  
mike@workstation:~/workspace/Beast$ clang++ -msse4.2 -dM -E - < /dev/null | egrep "SSE|AVX" | sort  
#define __SSE__ 1  
#define __SSE2__ 1  
#define __SSE2_MATH__ 1  
#define __SSE3__ 1  
#define __SSE4_1__ 1  
#define __SSE4_2__ 1  
#define __SSE_MATH__ 1  
#define __SSSE3__ 1  
  
mike@workstation:~/workspace/Beast$ g++ -msse4.2 -dM -E - < /dev/null | egrep "SSE|AVX" | sort  
#define __SSE__ 1  
#define __SSE2__ 1  
#define __SSE2_MATH__ 1  
#define __SSE3__ 1  
#define __SSE4_1__ 1  
#define __SSE4_2__ 1  
#define __SSE_MATH__ 1  
#define __SSSE3__ 1  
```  
  
So what about this?  
  
```  
#ifndef BEAST_NO_INTRINSICS  
# if defined(_MSC_VER) || defined(__SSE4_2__)  
#  define BEAST_NO_INTRINSICS 0  
# else  
#  define BEAST_NO_INTRINSICS 1  
# endif  
#endif  
```

---

## Comment 36

> Username: vinniefalco  
> Created at: 2017-07-06 19:39:26 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-313498066  

I'm in favor of anything that works :)

---

## Comment 37

> Username: octopus-prime  
> Created at: 2017-07-06 19:42:07 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-313498715  

Here it works fine...  
So i could prepare a new pull request. Branch 74?

---

## Comment 38

> Username: mika-fischer  
> Created at: 2017-07-06 19:44:41 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-313499330  

@octopus-prime https://github.com/vinniefalco/Beast/issues/585#issuecomment-312804972 :-)

---

## Comment 39

> Username: vinniefalco  
> Created at: 2017-07-09 03:46:31 UTC  
> Url: https://github.com/boostorg/beast/issues/585#issuecomment-313896363  

The code had to be turned off due to licensing issues anyway. It will come back later in the year.
