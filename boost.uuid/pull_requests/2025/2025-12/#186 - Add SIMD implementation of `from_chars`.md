# #186 Add SIMD implementation of `from_chars` [Merged]

> Username: Lastique  
> Created at: 2025-12-31 18:41:58 UTC  
> Updated at: 2026-01-06 13:04:45 UTC  
> Merged at: 2026-01-05 13:53:08 UTC  
> Closed at: 2026-01-05 13:53:08 UTC  
> Url: https://github.com/boostorg/uuid/pull/186  

This adds SSE4.1, AVX2, AVX-512v1 and AVX10.1 implementations of the `from_chars` algorithm. The generic implementation is moved to its own header and `constexpr` is relaxed to only enabled when `is_constant_evaluated`  
is supported.  
  
The performance effect on Intel Golden Cove (Core i7-12700K), gcc 13.3, in millions of successful `from_chars()` calls per second:  
  
```  
Char     | Generic | SSE4.1          | AVX2            | AVX10.1  
=========+=========+=================+=================+================  
char     |  47.201 | 193.321 (4.10x) | 172.585 (3.66x) | 191.159 (4.05x)  
char16_t |  42.567 | 452.366 (10.6x) | 343.471 (8.07x) | 385.817 (9.06x)  
char32_t |  43.741 | 378.824 (8.66x) | 300.675 (6.87x) | 332.751 (7.61x)  
```  
  
The AVX2 version is slightly slower than SSE4.1 because on Intel microarchitectures the VEX-coded `vpblendvb` instruction is slower than the legacy SSE4.1 `pblendvb`. The performance could be improved by using asm blocks to force using `pblendvb` in AVX2 code, but this may potentially cause SSE/AVX transition penalties if the target vector register happens to have "dirty" upper bits. There's no way to ensure this doesn't happen, so this is not implemented. According to uops.info, AMD Zen 4 should not have this problem (both instructions have the same performance numbers).  
AVX10.1 claws back some performance (and should also be slightly faster on Zen 4 due to the faster `vpermi2b`; not tested though).  
  
The unsuccessful parsing case depends on where the error happens, as the generic version may terminate sooner if the error is detected at the beginning of the input string, while the SIMD version performs roughly the same amount of work but faster. Here are some examples for 8-bit character types (for larger types the numbers are more or less comparable):  
  
```  
Error              | Generic  | SSE4.1          | AVX2            | AVX10.1  
===================+==========+=================+=================+================  
EOI at 35 chars    |   49.997 | 330.011 (6.60x) | 277.227 (5.54x) | 306.330 (6.13x)  
EOI at 1 char      | 2647.954 | 430.265 (0.16x) | 313.430 (0.12x) | 411.083 (0.16x)  
Missing dash at 23 |   81.985 | 519.764 (6.34x) | 381.585 (4.65x) | 476.974 (5.82x)  
Missing dash at 8  |  222.022 | 509.178 (2.29x) | 380.894 (1.72x) | 476.111 (2.14x)  
Illegal char at 35 |   47.728 | 356.977 (7.48x) | 282.861 (5.93x) | 310.955 (6.52x)  
Illegal char at 0  | 1765.537 | 400.938 (0.23x) | 323.588 (0.18x) | 353.294 (0.20x)  
```  
  
In general, only the very early errors tend to perform worse in the SIMD version and the majority of cases are still faster.  
  
The test code that was used for benchmarking is here:  
  
[uuid_from_chars_perftest.cpp](https://github.com/user-attachments/files/24397159/uuid_from_chars_perftest.cpp)  
  
Compile and run with:  
  
```  
g++ -O3 -march=x86-64 -std=gnu++17 -I. -o uuid_from_chars_perftest_generic uuid_from_chars_perftest.cpp  
g++ -O3 -march=nehalem -std=gnu++17 -I. -o uuid_from_chars_perftest_sse41 uuid_from_chars_perftest.cpp  
g++ -O3 -march=haswell -std=gnu++17 -I. -o uuid_from_chars_perftest_avx2 uuid_from_chars_perftest.cpp  
g++ -O3 -march=icelake-client -std=gnu++17 -I. -o uuid_from_chars_perftest_avx10_1 uuid_from_chars_perftest.cpp  
  
taskset --cpu-list 12 ./uuid_from_chars_perftest_generic  
taskset --cpu-list 12 ./uuid_from_chars_perftest_sse41  
taskset --cpu-list 12 ./uuid_from_chars_perftest_avx2  
taskset --cpu-list 12 ./uuid_from_chars_perftest_avx10_1  
```  
  
`taskset` is used to lock the test execution to a single core to make the test results more stable. Adjust according to your system.  
  
🎄🎁

---

## Comment 1

> Username: Lastique  
> Created_at: 2025-12-31 18:45:46 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3702698348  

I may still experiment with the unsuccessful parsing part (specifically, the partial loads on different instruction sets), but probably after a couple of weeks. Other than that it should be more or less ready for review.

---

## Comment 2

> Username: pdimov  
> Created_at: 2026-01-01 01:55:44 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3703158673  

The obvious question here is, if SSE 4.1 is in all cases faster, why do we need to use AVX at all?

---

## Comment 3

> Username: Lastique  
> Created_at: 2026-01-01 03:11:03 UTC  
> Updated_at: 2026-01-01 03:13:53 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3703204572  

I have updated the implementation:  
- Added workarounds for the slow `vpblendvb` on Intel. The workarounds do have some performance overhead compared to SSE4.1, but are still faster than using `vpblendvb`.  
- Removed partial loads AVX2 implementation, use SSE4.1 version up until AVX-512. The SSE4.1 version turned out to be faster than `vpmaskmovd`+`vblendvps`.  
- Fixed incorrect constants for the non-ASCII-compatible character encodings. Reorganized character code constants for better clarity.  
  
Performance numbers updated (improved for AVX2, for both successful and failed cases). Updated numbers are:  
  
Successful parse:  
  
```  
Char     | Generic | SSE4.1          | AVX2            | AVX10.1  
=========+=========+=================+=================+================  
char     |  42.248 | 192.775 (4.56x) | 189.966 (4.50x) | 189.043 (4.47x)  
char16_t |  42.526 | 452.540 (10.6x) | 403.592 (9.49x) | 385.550 (9.07x)  
char32_t |  43.676 | 378.480 (8.67x) | 315.015 (7.21x) | 333.656 (7.64x)  
```  
  
Failed parse:  
  
```  
Error              | Generic  | SSE4.1          | AVX2            | AVX10.1  
===================+==========+=================+=================+================  
EOI at 35 chars    |   50.047 | 338.083 (6.76x) | 317.576 (6.35x) | 306.542 (6.13x)  
EOI at 1 char      | 2892.682 | 428.000 (0.15x) | 383.142 (0.13x) | 413.899 (0.14x)  
Missing dash at 23 |   82.507 | 523.848 (6.35x) | 456.132 (5.53x) | 472.757 (5.73x)  
Missing dash at 8  |  220.119 | 512.046 (2.33x) | 455.508 (2.07x) | 475.703 (2.16x)  
Illegal char at 35 |   47.632 | 357.264 (7.50x) | 315.881 (6.63x) | 309.569 (6.50x)  
Illegal char at 0  | 1762.425 | 403.983 (0.23x) | 360.107 (0.20x) | 351.822 (0.20x)  
```  
  
[uuid_from_chars_perftest.txt](https://github.com/user-attachments/files/24398929/uuid_from_chars_perftest.txt)  
  
There's a bit of run-to-run variance, and I regularly see AVX2 and AVX10.1 switch places. SSE4.1 is still slightly ahead on average, but again, AVX variants win from one run to another.  
  
> The obvious question here is, if SSE 4.1 is in all cases faster, why do we need to use AVX at all?  
  
The problem is simply recompiling the same SSE4.1 code for AVX will give you slower code. The SSE4.1 `pblendvb` will get replaced by AVX2 `vpblendvb`, which is slower on Intel. As noted above, I've applied workarounds, but they are still not free.  
  
AVX10.1 is useful because it uses less instructions (i.e. smaller code) while giving about the same or slightly better performance.  
  
I also suspect that AMD Zen 4 should have better performance for AVX10.1 because it has faster `vpermi2b`. It also has fast `vpblendvb` and wouldn't need the workarounds, but they shouldn't harm it too much. Unfortunately, I don't have AMD hardware to test.

---

## Comment 4

> Username: pdimov  
> Created_at: 2026-01-01 03:28:36 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3703215940  

Did you see the Drone UBSAN failure on the old macOS? Is that a genuine false positive, or something more important?

---

## Comment 5

> Username: Lastique  
> Created_at: 2026-01-01 03:33:32 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3703218820  

> Did you see the Drone UBSAN failure on the old macOS? Is that a genuine false positive, or something more important?  
  
The error is triggered by an unaligned load, which is intentional and can happen. Unaligned loads and stores are allowed in x86 and the code leverages that.  
  
Is there a way to suppress those checks for SIMD code?

---

## Comment 6

> Username: pdimov  
> Created_at: 2026-01-01 05:05:10 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3703270054  

That's disabling way too much. You only need it on the `int*` loads (and then only on some presumably old Clangs).

---

## Comment 7

> Username: Lastique  
> Created_at: 2026-01-01 09:40:22 UTC  
> Updated_at: 2026-01-01 09:42:57 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3703475911  

Unaligned `int*` load is just what the test happened to trigger, it's not the only UB that can happen. Unaligned loads and stores are used regardless of the clang version. Besides that, there are aliasing violations, possible integer overflows (e.g. due to conversions when calling intrinsics), out-of-bound array indexing (in masked loads) and possibly more. There's little point in running UB checks on a low level code like this.

---

## Comment 8

> Username: pdimov  
> Created_at: 2026-01-01 11:56:33 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3703619481  

The sanitizer understands all that. The newer one also understands that the `_mm_cvtsi32_si128` intrinsic can read unaligned through `int*`.  
  
I just don't like `__attribute__((no_sanitize("undefined")))` as a matter of principle. It's tolerable to put it here and there in the same manner we disable warnings locally, but having this in a common header and then using it pervasively and completely unnecessarily doesn't sit well with me.

---

## Comment 9

> Username: pdimov  
> Created_at: 2026-01-01 12:12:07 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3703632898  

Wait, no, I'm wrong, the `int*` access happens before calling the intrinsic and all Clang versions diagnose it. Looks like this configuration was the only one to happen to read through an unaligned pointer. https://godbolt.org/z/P3a8xj6f3  
  
GCC doesn't in a simple example, but that's because it's smart enough to see the unaligned load and align the initial char array so that the load becomes aligned. Amazing. https://godbolt.org/z/KdzKo96WM https://godbolt.org/z/KaP11Pd7o  
  
Before fixing this, we probably need to have a test exercising unaligned loads/stores specifically so we that know what we're fixing.  
  
Let's keep this PR without the sanitization-related changes so that I can merge it without any doubts, and fix the ubsan issues in a separate one.

---

## Comment 10

> Username: pdimov  
> Created_at: 2026-01-01 12:21:03 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3703641249  

`movdqu` is not a problem, both GCC and Clang understand it. https://godbolt.org/z/ET5obhEW5

---

## Comment 11

> Username: Lastique  
> Created_at: 2026-01-01 16:23:41 UTC  
> Updated_at: 2026-01-01 16:29:36 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3703871164  

> Let's keep this PR without the sanitization-related changes so that I can merge it without any doubts, and fix the ubsan issues in a separate one.  
  
Ok, I removed the UBSAN changes. Also extended the `vpblendvb` workarounds to AVX since the 128-bit instruction is used already with AVX.  
  
> Wait, no, I'm wrong, the `int*` access happens before calling the intrinsic  
  
Yes, there are places like [this](https://github.com/Lastique/uuid/blob/af01f031f1bae3a9a1701fb9c95a71fd41d34a75/include/boost/uuid/detail/to_chars_x86.hpp#L202) and [this](https://github.com/Lastique/uuid/blob/af01f031f1bae3a9a1701fb9c95a71fd41d34a75/include/boost/uuid/detail/from_chars_x86.hpp#L493), and also [this](https://github.com/Lastique/uuid/blob/af01f031f1bae3a9a1701fb9c95a71fd41d34a75/include/boost/uuid/detail/from_chars_x86.hpp#L512). `movd`, `movq`, `pinsr*`, `pextr*` and also all moves between GPRs and memory don't require an aligned memory location, which is what the code takes advantage of.  
  
For out-of-bounds indexing, there's this [instance](https://github.com/Lastique/uuid/blob/af01f031f1bae3a9a1701fb9c95a71fd41d34a75/include/boost/uuid/detail/from_chars_x86.hpp#L624), for example. Note that whether the memory access happens is irrelevant; in C++ a mere pointer adjustment beyond the array boundary is formally UB.  
  
Whether the code triggers an UBSAN error depends on how the code is used (e.g. with aligned or unaligned buffers, with small or large buffers, etc.) Also, I think it is reasonable to expect users to run UBSAN on their code involving Boost.UUID, so even if we don't trigger something in our tests, they might. x86 vector intrinsics are not really defined with C++ conformance in mind, and SIMD code in general tend to be low level and beyond limitations imposed by C++ abstract machine (i.e. the code normally treats bags of bits within real hardware limitations rather than objects and lifetime within the C++ object model). So triggering something that is formally UB in C++ is almost inevitable.

---

## Comment 12

> Username: pdimov  
> Created_at: 2026-01-01 16:34:16 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3703880591  

The problem here is that this is genuine undefined behavior; UBSAN is not giving false positives. And if it's genuine undefined behavior, then it's busted because the optimizer may decide to do bad things to it.  
  
The legitimate way to read an unaligned `int` from memory is with `memcpy`, not `*(int*)p`. This generates the same code because the compiler knows that `mov` can read unaligned and uses it.  
  
https://godbolt.org/z/xvvMTrr4a https://godbolt.org/z/nzovoYqqT https://godbolt.org/z/69Y5xMxqv

---

## Comment 13

> Username: Lastique  
> Created_at: 2026-01-01 17:03:07 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3703903683  

> The problem here is that this is genuine undefined behavior; UBSAN is not giving false positives.  
  
It is, in the sense C++ standard doesn't define what happens in this case. Supporting x86 intrinsics require the compiler to define behavior beyond what the C++ standard defines, otherwise the intrinsics would be unimplementable. In particular, the compiler must support type punning, which allows treating data as bags of bits. No sane compiler would do "bad things" if it sees that code is operating on bags of bits (as indicated by `BOOST_MAY_ALIAS`).  
  
In more practical terms, the C++ standard doesn't define the behavior in this case because different hardware implementations may have varying behaviors on unaligned memory accesses. But this code is written specifically for x86, and we know its behavior.  
  
> The legitimate way to read an unaligned `int` from memory is with `memcpy`, not `*(int*)p`.  
  
I can change the code to use `memcpy`, although that would complicate it a bit and make it less efficient in debug mode (due to the added temporary variables). The latter is probably not very important.  
  
This won't fix other UB problems, though.

---

## Comment 14

> Username: pdimov  
> Created_at: 2026-01-01 17:12:38 UTC  
> Updated_at: 2026-01-01 17:13:26 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3703913070  

> In more practical terms, the C++ standard doesn't define the behavior in this case because different hardware implementations may have varying behaviors on unaligned memory accesses. But this code is written specifically for x86, and we know its behavior.  
  
That's not how undefined behavior works. When the standard says "the behavior of code that does this is undefined", this means that the behavior is explicitly undefined even if you know that it's always executed on a PDP-11. That's because the compiler optimizer operates under the assumption that only code with defined behavior matters, and it runs on a target-independent intermediate representation.  
  
There are exceptions but as a general rule, undefined behavior (in the above sense, not in the sense of "the standard does not define the behavior of `_loadu_si128`") is best avoided.  
  
> This won't fix other UB problems, though.  
  
The only other instance is `p + 8` and I'm not sure what we can do about it, or whether we need to. It's hard to say. UBSAN is probably not going to bother us about it, and turning it off with an attribute isn't going to help with anything else anyway.

---

## Comment 15

> Username: pdimov  
> Created_at: 2026-01-01 17:23:40 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3703924867  

> I can change the code to use memcpy, although that would complicate it a bit and make it less efficient in debug mode (due to the added temporary variables).  
  
It introduces one extra `int` copy but that's probably the least of the worries in debug mode. Interestingly, `__builtin_memcpy` lowers to `mov` even in debug on both GCC and Clang.

---

## Comment 16

> Username: Lastique  
> Created_at: 2026-01-01 22:54:24 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3704204213  

I've added `__builtin_memcpy` etc. to cstring.hpp and switched all uses of `memcpy` etc. to the functions from cstring.hpp. And updated to_chars_x86.hpp and from_chars_x86.hpp to use helpers from endian.hpp (which themselves use `memcpy`) to load and store integers.

---

## Comment 17

> Username: Lastique  
> Created_at: 2026-01-02 02:11:44 UTC  
> Updated_at: 2026-01-02 02:28:11 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3704332638  

I've been puzzled why the 8-bit character type performance numbers are so low compared to 16 and 32-bit, as I would have expected for them to be actually highest of the three. I don't have a good answer to that, but I found that for some reason the first test that gets to run produces noticeably lower results than the rest, and the `char` test happened to be run the first. Not sure why, perhaps the warmup wasn't enough.  
  
I have reworked the performance test to run the successful parse twice (the first run as a warmup, with the performance numbers discarded) and increased the loop counts to improve results stability. No changes to the `from_chars` implementation (other than switching to `memcpy`). Here are the updated performance numbers:  
  
Successful parse:  
  
```  
Char     | Generic | SSE4.1          | AVX2            | AVX10.1  
=========+=========+=================+=================+================  
char     |  38.872 | 530.234 (13.6x) | 459.817 (11.8x) | 470.393 (12.1x)  
char16_t |  39.053 | 453.091 (11.6x) | 396.027 (10.1x) | 387.651 (9.93x)  
char32_t |  50.448 | 372.173 (7.38x) | 340.030 (6.74x) | 327.137 (6.48x)  
```  
  
Failed parse:  
  
```  
Error              | Generic  | SSE4.1          | AVX2            | AVX10.1  
===================+==========+=================+=================+================  
EOI at 35 chars    |   43.244 | 331.902 (7.68x) | 308.218 (7.13x) | 302.104 (6.99x)  
EOI at 1 char      | 2671.939 | 423.610 (0.16x) | 392.422 (0.15x) | 407.631 (0.15x)  
Missing dash at 23 |   74.125 | 509.295 (6.87x) | 454.657 (6.13x) | 468.960 (6.33x)  
Missing dash at 8  |  218.293 | 505.204 (2.31x) | 454.500 (2.08x) | 469.700 (2.15x)  
Illegal char at 35 |   47.509 | 354.695 (7.47x) | 318.311 (6.70x) | 303.021 (6.38x)  
Illegal char at 0  | 1731.302 | 407.960 (0.24x) | 363.393 (0.21x) | 347.442 (0.20x)  
```  
  
[uuid_from_chars_perftest.txt](https://github.com/user-attachments/files/24403881/uuid_from_chars_perftest.txt)  
[uuid_from_chars_perftest.cpp](https://github.com/user-attachments/files/24403882/uuid_from_chars_perftest.cpp)  
  
The most notable change is the `char` row in the successful parse, which is more in line with my expectations. The rest is more or less unchanged.

---

## Comment 18

> Username: Lastique  
> Created_at: 2026-01-02 18:32:48 UTC  
> Updated_at: 2026-01-02 18:35:54 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3705995148  

Added a few minor optimizations in input string validation tests, which improved overall performance a little.  
  
I've been investigating the AVX10.1 performance, which is lower than AVX2 in some cases. It turns out, `vpermi2b`/`vpermi2t` instructions from AVX512_VBMI that are used [here](https://github.com/Lastique/uuid/blob/680d7709b8d2956632eec03d169c08b1d9f0b33b/include/boost/uuid/detail/from_chars_x86.hpp#L720-L721) to move the hex digits into place are rather slow on Intel. So much slow that the AVX2/AVX512v1 code path ends up being faster. On the other hand, these instructions are much faster on AMD Zen 4 and also reduce the number of vector constants from 4 to 2 in this code segment. I don't have an AMD system to test, though. To make matters worse, clang 18 sometimes generates `vpermi2b`/`vpermi2t` instructions by itself for the AVX512v1 code path, when AVX512_VBMI is enabled (which, of course, tanks performance). I'm not sure what to do about this. Should we keep this branch? Make it conditional?  
  
Here are the updated performance numbers, including AVX512v1 (which was compiled with `-march=skylake-avx512` and doesn't use `vpermi2b`):  
  
Successful parse:  
  
```  
Char     | Generic | SSE4.1          | AVX2            | AVX512v1        | AVX10.1  
=========+=========+=================+=================+=================+================  
char     |  38.571 | 560.645 (14.5x) | 501.505 (13.0x) | 540.038 (14.0x) | 480.778 (12.5x)  
char16_t |  37.998 | 479.308 (12.6x) | 425.728 (11.2x) | 416.379 (11.0x) | 392.326 (10.3x)  
char32_t |  50.327 | 391.313 (7.78x) | 359.312 (7.14x) | 349.849 (6.95x) | 333.979 (6.64x)  
```  
  
Failed parse:  
  
```  
Error              | Generic  | SSE4.1          | AVX2            | AVX512v1        | AVX10.1  
===================+==========+=================+=================+=================+================  
EOI at 35 chars    |   43.629 | 356.562 (8.17x) | 326.311 (7.48x) | 322.377 (7.39x) | 308.155 (7.06x)  
EOI at 1 char      | 2645.783 | 444.769 (0.17x) | 400.275 (0.15x) | 404.826 (0.15x) | 403.730 (0.15x)  
Missing dash at 23 |   73.878 | 514.303 (6.96x) | 474.694 (6.43x) | 507.949 (6.88x) | 474.077 (6.42x)  
Missing dash at 8  |  223.921 | 516.641 (2.31x) | 472.737 (2.11x) | 506.242 (2.26x) | 473.718 (2.12x)  
Illegal char at 35 |   47.373 | 368.002 (7.77x) | 333.233 (7.03x) | 318.242 (6.72x) | 301.659 (6.37x)  
Illegal char at 0  | 1729.087 | 421.511 (0.24x) | 385.217 (0.22x) | 374.047 (0.22x) | 351.944 (0.20x)  
```  
[uuid_from_chars_perftest.txt](https://github.com/user-attachments/files/24410866/uuid_from_chars_perftest.txt)

---

## Comment 19

> Username: pdimov  
> Created_at: 2026-01-02 20:16:33 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3706158317  

If it were me, I'd just route everything to SSE 4.1 without much hesitation, because I don't like extra work and complexity for negative benefit.  
  
On the other hand, though, the work is already done, and maybe two years from now the AVX paths will be faster. So I don't know.  
  
How does SSE 4.1 perform when AVX2 is available and the compiler helpfully rewrites things to be slower?  
  
Or, more generally, how do all of the paths perform when the higher instruction sets are available? (The success case only.)

---

## Comment 20

> Username: Lastique  
> Created_at: 2026-01-02 21:46:42 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3706278643  

I have `#undef`ed all the AVX macros in config.hpp (i.e. the SSE4.1 branch is used everywhere), recompiled and run the tests:  
  
```  
Char     | Generic | SSE4.1          | AVX2            | AVX512v1        | AVX10.1  
=========+=========+=================+=================+=================+================  
char     |  38.513 | 561.199 (14.6x) | 411.391 (10.7x) | 518.060 (13.4x) | 518.060 (13.4x)  
char16_t |  38.779 | 478.396 (12.3x) | 354.794 (9.15x) | 428.273 (11.0x) | 428.677 (11.0x)  
char32_t |  50.352 | 391.831 (7.78x) | 311.329 (6.18x) | 343.293 (6.82x) | 342.708 (6.81x)  
```  
  
AVX2 and AVX512v1 are noticeably worse.

---

## Comment 21

> Username: pdimov  
> Created_at: 2026-01-02 21:54:02 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3706287651  

Yes, AVX2 suffers a lot. AVX512v1 is about the same. AVX10.1 speeds up :-)  
  
How does the AVX2 path fare when AVX512v1 or the AVX10.1 instruction sets are available?

---

## Comment 22

> Username: Lastique  
> Created_at: 2026-01-02 22:03:05 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3706298233  

Undefing just AVX512_V1 and AVX10_1 (i.e. code paths up to AVX2 are enabled) results in this:  
  
```  
Char     | Generic | SSE4.1          | AVX2            | AVX512v1        | AVX10.1  
=========+=========+=================+=================+=================+================  
char     |  38.513 | 561.199 (14.6x) | 501.293 (13.0x) | 526.865 (13.7x) | 525.282 (13.6x)  
char16_t |  38.779 | 478.396 (12.3x) | 426.468 (11.0x) | 444.354 (11.5x) | 447.091 (11.5x)  
char32_t |  50.352 | 391.831 (7.78x) | 359.547 (7.14x) | 368.286 (7.31x) | 368.175 (7.31x)  
```  
  
Undefing just AVX10_1 would give you basically what I posted [earlier](https://github.com/boostorg/uuid/pull/186#issuecomment-3705995148), with the AVX10.1 column equal to AVX512v1.

---

## Comment 23

> Username: pdimov  
> Created_at: 2026-01-02 22:14:33 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3706313399  

The AVX2 path seems to be good enough (almost dominating except for the char/AVX512v1 case) for any AVX. And since we can't drop it because SSE 4.1 is unacceptably bad with -mavx2, it seems that the minimum we can use is SSE 4.1 when AVX2 isn't available, and AVX2 otherwise.  
  
Is there a difference on Clang, or is it more or less the same? I assume this is GCC.

---

## Comment 24

> Username: Lastique  
> Created_at: 2026-01-02 22:36:54 UTC  
> Updated_at: 2026-01-02 22:38:34 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3706379725  

> Undefing just AVX10_1 would give you basically what I posted [earlier](https://github.com/boostorg/uuid/pull/186#issuecomment-3705995148), with the AVX10.1 column equal to AVX512v1.  
  
Although, no, not with clang, as it generates `vpermi2b` by itself. The clang 18.1.3 numbers are this:  
  
All code paths enabled:  
  
```  
Char     | Generic | SSE4.1          | AVX2            | AVX512v1        | AVX10.1  
=========+=========+=================+=================+=================+================  
char     |  40.146 | 518.183 (12.9x) | 494.403 (12.3x) | 534.885 (13.3x) | 455.369 (11.3x)  
char16_t |  35.486 | 440.987 (12.4x) | 414.580 (11.7x) | 411.387 (11.6x) | 368.167 (10.4x)  
char32_t |  42.507 | 358.356 (8.43x) | 340.481 (8.01x) | 341.572 (8.04x) | 328.252 (7.72x)  
```  
  
AVX10.1 code path disabled:  
  
```  
Char     | Generic | SSE4.1          | AVX2            | AVX512v1        | AVX10.1  
=========+=========+=================+=================+=================+================  
char     |  40.146 | 518.183 (12.9x) | 494.403 (12.3x) | 534.885 (13.3x) | 488.010 (12.2x)  
char16_t |  35.486 | 440.987 (12.4x) | 414.580 (11.7x) | 411.387 (11.6x) | 387.831 (10.9x)  
char32_t |  42.507 | 358.356 (8.43x) | 340.481 (8.01x) | 341.572 (8.04x) | 331.946 (7.81x)  
```  
  
Notice that AVX10.1 doesn't climb back to AVX512v1 values. In the tables below as well.  
  
AVX512v1 and AVX10.1 code paths disabled:  
  
```  
Char     | Generic | SSE4.1          | AVX2            | AVX512v1        | AVX10.1  
=========+=========+=================+=================+=================+================  
char     |  40.146 | 518.183 (12.9x) | 494.403 (12.3x) | 470.823 (11.7x) | 433.114 (10.8x)  
char16_t |  35.486 | 440.987 (12.4x) | 414.580 (11.7x) | 401.281 (11.3x) | 371.844 (10.5x)  
char32_t |  42.507 | 358.356 (8.43x) | 340.481 (8.01x) | 331.104 (7.79x) | 303.277 (7.13x)  
```  
  
AVX, AVX2, AVX512v1 and AVX10.1 code paths disabled:  
  
```  
Char     | Generic | SSE4.1          | AVX2            | AVX512v1        | AVX10.1  
=========+=========+=================+=================+=================+================  
char     |  40.146 | 518.183 (12.9x) | 414.773 (10.3x) | 513.215 (12.8x) | 446.692 (11.1x)  
char16_t |  35.486 | 440.987 (12.4x) | 351.771 (9.91x) | 426.607 (12.0x) | 369.061 (10.4x)  
char32_t |  42.507 | 358.356 (8.43x) | 297.844 (7.01x) | 337.519 (7.94x) | 273.384 (6.43x)  
```  
  
> I assume this is GCC  
  
Yes, all of the tables in the previous comments were gcc 13.3.0.

---

## Comment 25

> Username: Lastique  
> Created_at: 2026-01-02 22:43:36 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3706398080  

I think, I'm going to disable the AVX10.1 path for now, until we have someone who can test it on an AMD CPU, or some newer Intel microarchitecture improves `vpermi2b`. The AVX512v1 path still provides some nice performance boost, so I'll keep it enabled. Alas, clang ruins it, but there's little I can do about it.

---

## Comment 26

> Username: pdimov  
> Created_at: 2026-01-03 17:45:16 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3707234230  

I'm not sure why we need all these `std::memcpy` -> `detail::memcpy` changes everywhere, e.g. in `time_generator_v1.hpp`. Even if it mattered for performance, and it probably doesn't there, the compilers already do that by themselves.

---

## Comment 27

> Username: Lastique  
> Created_at: 2026-01-04 07:11:33 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3707810658  

I want to reduce the possibility of `memcpy` function calls in vectorized code as much as possible, especially in AVX. Including in debug mode. This necessitates the changes is endian.hpp. The other places I changed mostly for consistency and to remove `#include <cstring>`. I don't think we want actual function calls anywhere, do we? I can change the other places back, if you want.

---

## Comment 28

> Username: pdimov  
> Created_at: 2026-01-04 09:55:33 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3707926400  

I'm fine with the vectorized code using the builtins, even though I don't think it's necessary as I haven't seen any GCC or Clang not treating a call to `std::memcpy` as a call to `__builtin_memcpy`. (Note how it's possible to call `std::memcpy` in `constexpr`, but you couldn't call your own replacements when they weren't.)  
  
But I don't want the other, unrelated, changes.

---

## Comment 29

> Username: pdimov  
> Created_at: 2026-01-04 10:31:23 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3707953519  

Thanks.  
  
Since the `memset` replacement isn't used anywhere, and the additional `memcmp` function isn't used by the vectorized code, I'd prefer them to not be added.

---

## Comment 30

> Username: Lastique  
> Created_at: 2026-01-04 10:33:52 UTC  
> Updated_at: 2026-01-04 10:37:40 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3707955037  

I've changed back the other instances to `std::memcpy`.  
  
> Note how it's possible to call `std::memcpy` in `constexpr`, but you couldn't call your own replacements when they weren't.  
  
You can mention both `std::memcpy` and `__builtin_memcpy` in constexpr functions. I think, clang even allows `__builtin_memcpy` to be actually called in a constexpr context in some cases (but not `std::memcpy`). You can't mention `detail::memcpy` in a constexpr function, if its *definition* is visible and not constexpr, even if the call never happens in a constexpr context. (I think, this is either a compiler bug or a standard defect.) If it is constexpr then some compilers fail as they check whether there is any possibility for the function to participate in a constant expression (which it isn't, definitely not if it forwards to `std::memcpy`).  
  
So, `__builtin_memcpy` is not less constexpr-friendly, possibly the opposite, and `detail::memcpy` (the way it is defined now) is as friendly as `__builtin_memcpy` is.

---

## Comment 31

> Username: pdimov  
> Created_at: 2026-01-04 10:43:29 UTC  
> Updated_at: 2026-01-04 10:44:17 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3707961920  

That's my point. If `std::memcpy` were not equivalent to `__builtin_memcpy`, you wouldn't be able to call it in a `constexpr` function for the same reason you can't call another non-constexpr function.  
  
But you can, which means that it's always replaced with `__builtin_memcpy` by the compiler frontend.

---

## Comment 32

> Username: Lastique  
> Created_at: 2026-01-04 10:50:51 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3707967502  

Removed `memset` and `detail::memcpy`.  
  
You can *mention* any function as long as it is not defined or the definition is constexpr. With clang, you can *call* `__builtin_memcpy` sometimes. Also, gcc is more eager to inline the builtin than the libc call. So clearly the builtin is not the same as `std::memcpy`.

---

## Comment 33

> Username: pdimov  
> Created_at: 2026-01-04 10:52:05 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3707968315  

> I think, clang even allows `__builtin_memcpy` to be actually called in a constexpr context in some cases (but not `std::memcpy`).  
  
Yes, `__builtin_memcpy` is `constexpr` starting from Clang 8. https://godbolt.org/z/xvon8v4TP  
  
But not on GCC, so this doesn't help in portable code.

---

## Comment 34

> Username: pdimov  
> Created_at: 2026-01-04 10:58:45 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3707972526  

> You can _mention_ any function as long as it is not defined or the definition is constexpr.  
  
Ah. Yes, you're correct. It's because `std::memcpy` is only declared, but not defined. https://godbolt.org/z/jnajsE46r  
  
So it's not replaced by the frontend, but somewhere later.  
  
Is this ready to merge now, or are you still making changes?

---

## Comment 35

> Username: pdimov  
> Created_at: 2026-01-04 11:29:52 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3708000186  

Is it possible to make this (and/or `to_chars`) work when only SSE2 is available? This is the most common case because most people don't override the target architecture.

---

## Comment 36

> Username: pdimov  
> Created_at: 2026-01-04 18:59:38 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3708336029  

I've added GHA jobs exercising `/arch:AVX` and `/arch:AVX2` for msvc and clang-cl, which yields the following failures  
```  
.\boost/uuid/detail/to_chars_x86.hpp(195): error C2220: the following warning is treated as an error  
.\boost/uuid/detail/to_chars_x86.hpp(195): warning C4127: conditional expression is constant  
```  
but I'm not fixing these because they intersect with changes in this PR.  
  
During this I also discovered that MSVC now supports `/arch:SSE4.2` in 64 bit,  
  
https://learn.microsoft.com/en-us/cpp/build/reference/arch-x64?view=msvc-170  
https://developercommunity.visualstudio.com/t/Add-arch:SSE42-level-to-allow-optimiz/10664771?sort=newest  
  
but there's no predefined macro that can be used to detect it. There's a constexpr intrinsic,  
  
https://learn.microsoft.com/en-us/cpp/intrinsics/check-isa-arch-support?view=msvc-170  
  
but I don't think it works for us.

---

## Comment 37

> Username: Lastique  
> Created_at: 2026-01-05 11:40:53 UTC  
> Updated_at: 2026-01-05 11:46:04 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3710086683  

> Is this ready to merge now, or are you still making changes?  
  
At this point I'm only addressing comments.  
  
> Is it possible to make this (and/or `to_chars`) work when only SSE2 is available? This is the most common case because most people don't override the target architecture.  
  
The implementation of both algorithms rely heavily on `pshufb` from SSSE3, which is difficult to emulate in general. Maybe it won't be as terrible for the specific use cases in these algorithms. `from_chars` also uses blends and `packusdw` from SSE4.1, which are easier, but still not quite cheap. I can do this, but I'm not sure how much performance benefit will be retained. I'm also not sure when I'll be able to test it as I currently don't have reliable access to the system I was testing on previously. Tell me if you want me to do this.  
  
> I've added GHA jobs exercising `/arch:AVX` and `/arch:AVX2` for msvc and clang-cl, which yields the following failures  
  
I've silenced these warnings.  
  
> There's a constexpr intrinsic, but I don't think it works for us.  
  
Yes. I need a preprocessor macro to define different constants and function signatures. Also, the intrinsic only detects SSE2 and SSE4.2 (not 4.1 or anything in between), and MSVC already defines macros for AVX versions, so the intrinsic is pretty useless.

---

## Comment 38

> Username: Lastique  
> Created_at: 2026-01-05 11:54:08 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3710124380  

Actually, the way `pshufb` is used in `to_chars`, the shuffle pattern depends on the input bytes, so it is pretty difficult to emulate. A different approach would be needed there.

---

## Comment 39

> Username: pdimov  
> Created_at: 2026-01-05 13:47:27 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3710491040  

> Also, the intrinsic only detects SSE2 and SSE4.2 (not 4.1 or anything in between), and MSVC already defines macros for AVX versions, so the intrinsic is pretty useless.  
  
Well, it's not useless for detecting `/arch:SSE4.2`. :-)

---

## Comment 40

> Username: pdimov  
> Created_at: 2026-01-05 14:09:05 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3710584098  

We can in principle, instead of this,  
```  
template<class Ch>  
BOOST_UUID_CXX14_CONSTEXPR_RT inline  
from_chars_result<Ch> from_chars( Ch const* first, Ch const* last, uuid& u ) noexcept  
{  
#if defined(BOOST_UUID_USE_SSE41)  
    if( detail::is_constant_evaluated_rt() )  
    {  
        return detail::from_chars_generic( first, last, u );  
    }  
    else  
    {  
        return detail::from_chars_simd( first, last, u );  
    }  
#else  
    return detail::from_chars_generic( first, last, u );  
#endif  
}  
```  
do this  
```  
template<class Ch>  
BOOST_UUID_CXX14_CONSTEXPR_RT inline  
from_chars_result<Ch> from_chars( Ch const* first, Ch const* last, uuid& u ) noexcept  
{  
    if( detail::is_constant_evaluated_rt() || !__check_arch_support( __IA_SUPPORT_SSE42, 0 ) )  
    {  
        return detail::from_chars_generic( first, last, u );  
    }  
    else  
    {  
        return detail::from_chars_simd( first, last, u );  
    }  
}  
```  
Probably not worth it. Still, people seem to be interested in CPUs that have SSE4.2 and don't have AVX. Although I suppose in that case they can just define `BOOST_UUID_USE_SSE41` themselves?

---

## Comment 41

> Username: pdimov  
> Created_at: 2026-01-05 16:46:04 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3711241212  

I enabled `-Wconversion -Wsign-conversion` et al because Unordered uses them and we were causing failures there, and inserted the obvious static_casts in `to_chars_x86.hpp` and `from_chars_x86.hpp` to silence them. You can take a look if you like, if something isn't to your liking.

---

## Comment 42

> Username: Lastique  
> Created_at: 2026-01-06 10:21:34 UTC  
> Updated_at: 2026-01-06 10:33:01 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3714113952  

> We can in principle, instead of this, do this  
  
This wouldn't be correct because there were CPUs with SSE4.1 but not 4.2 and those would not be using the SIMD version. Also, you wouldn't be able to remove the preprocessor checks because other compilers define macros and `BOOST_UUID_USE_*` may be defined by users.  
  
> Still, people seem to be interested in CPUs that have SSE4.2 and don't have AVX.  
  
Yes, Intel was releasing Pentium and Celeron CPUs without AVX for a long long time, until Tiger Lake IIRC.  
  
> Although I suppose in that case they can just define `BOOST_UUID_USE_SSE41` themselves?  
  
Yes. That was the idea for allowing users to define those macros - because MSVC doesn't support SSE macros for some reason.  
  
> I enabled `-Wconversion -Wsign-conversion` et al because Unordered uses them and we were causing failures there, and inserted the obvious static_casts in `to_chars_x86.hpp` and `from_chars_x86.hpp` to silence them.  
  
That is fine, thanks.

---

## Comment 43

> Username: pdimov  
> Created_at: 2026-01-06 10:35:18 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3714176127  

> This wouldn't be correct because there were CPUs with SSE4.1 but not 4.2 and those would not be using the SIMD version.  
  
This doesn't matter. The intrinsic `__check_arch_support` detects the new `/arch:SSE4.2` option at compile time. There's no option for 4.1, so there's nothing to detect there.  
  
This is unlike the older `__check_isa_support`, which detected the capabilities of the CPU at runtime.

---

## Comment 44

> Username: Lastique  
> Created_at: 2026-01-06 10:45:23 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3714219191  

> > This wouldn't be correct because there were CPUs with SSE4.1 but not 4.2 and those would not be using the SIMD version.  
>   
> This doesn't matter. The intrinsic `__check_arch_support` detects the new `/arch:SSE4.2` option at compile time. There's no option for 4.1, so there's nothing to detect there.  
  
Historically, MSVC users would specify `/arch:SSE2` and then use the intrinsics for the more advanced SSE versions, when they knew they will run on CPUs with those extentions. This means the intrinsic would return false.  
  
You can't specify `/arch:SSE4.2` and then expect the executable to be runnable without SSE4.2 because the compiler is allowed to generate instructions from SSE4.2 on its own.

---

## Comment 45

> Username: pdimov  
> Created_at: 2026-01-06 10:54:19 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3714250434  

Why would I expect that?

---

## Comment 46

> Username: Lastique  
> Created_at: 2026-01-06 11:02:08 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3714274353  

> Why would I expect that?  
  
Well, otherwise I don't understand how would you use the intrinsic to detect SSE4.1 or anything below.

---

## Comment 47

> Username: pdimov  
> Created_at: 2026-01-06 11:06:49 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3714289391  

There's no `/arch` option for SSE4.1, so there's nothing to detect.

---

## Comment 48

> Username: pdimov  
> Created_at: 2026-01-06 12:21:23 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3714505592  

What happens today:  
  
* when the program is compiled without `/arch`, the generic implementations are used  
* when the program is compiled with `/arch:SSE4.2`, the generic implementations are used  
* when the program is compiled with `/arch:AVX`, the SSE 4.1 implementations are used  
  
The intrinsic allows us to detect the second case and use the SSE 4.1 implementation. Since the compiler is already allowed to use SSE4.2 instructions, we can too.  
  
I don't understand why you think we need to detect 4.1 in some manner, or under what circumstances.

---

## Comment 49

> Username: pdimov  
> Created_at: 2026-01-06 12:22:04 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3714507540  

To clarify, I'm not saying we have to do this, I just don't understand what you are saying.

---

## Comment 50

> Username: Lastique  
> Created_at: 2026-01-06 12:26:14 UTC  
> Updated_at: 2026-01-06 12:50:17 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3714519309  

I'm not detecting compiler switches, I'm detecting ISA extensions. And with MSVC, it isn't possible to detect anything between SSE2 and SSE4.2, if you count the intrinsic (which isn't really useful), or AVX. Meaning, if you're targeting CPUs that only feature SSE4.1, you can't detect that and therefore users have to enable usage of those ISA extensions manually.  
  
The intrinsic is pretty useless even if you use it as a proxy to enable all extensions below SSE4.2. You can add a separate code branch *in addition* to the preprocessor checks, but that wouldn't work in general for from_chars_x86.hpp because it uses macros as an indication of certain ISA extension availability, and the macros wouldn't be defined. Using the intrinsic instead isn't always possible because preprocessor checks are used in contexts where the intrinsic isn't allowed. And no, I'm not going to use the intrinsic even where it is allowed because (a) MSVC is the only compiler that supports it and (b) it doesn't support fine grained SSE detection anyway. It's too much mess and inconsistency, the code is already a patchwork of various code paths for different ISA extensions. So, if a user wants to target a CPU with SSE4.1 (but not anything later), he has no choice to enable it manually with a macro, and the intrinsic doesn't help with that.

---

## Comment 51

> Username: Lastique  
> Created_at: 2026-01-06 12:31:54 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3714535778  

If you want to optimize the case where the user doesn't enable SSE4.1 for some reason, I think it is more reasonable to add an SSE2 code path. Although I would say, if a user uses MSVC and doesn't bother with enabling ISA extensions higher than SSE2 then he either doesn't care or truly wants to be compatible with that ISA baseline.  
  
I'll see about SSE2 support in the coming days/weeks.

---

## Comment 52

> Username: Lastique  
> Created_at: 2026-01-06 12:37:23 UTC  
> Updated_at: 2026-01-06 12:37:39 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3714550895  

> * when the program is compiled with `/arch:AVX`, the SSE 4.1 implementations are used  
  
No, the compiler defines `__AVX__` in this case, so AVX code paths are used.  
  
Does MSVC define `__SSE4_2__` when `/arch:SSE4.2` is specified? If so, we could use that to enable SSE4.1.

---

## Comment 53

> Username: pdimov  
> Created_at: 2026-01-06 12:58:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3714609730  

> No, the compiler defines __AVX__ in this case, so AVX code paths are used.  
  
There are no AVX code paths in `to_chars`, although I see there is one in `from_chars`. All that time I assumed we were talking about AVX2, and your tables say AVX2. Odd.  
  
> Does MSVC define __SSE4_2__ when /arch:SSE4.2 is specified? If so, we could use that to enable SSE4.1.  
  
No, it doesn't. If it did, we wouldn't be talking about the intrinsic in the first place.  
  
> If you want to optimize the case where the user doesn't enable SSE4.1 for some reason  
  
I want to optimize the case where the user uses `/arch:SSE4.2`. I don't see how this can be construed as the user not enabling SSE4.1, because SSE4.2 implies SSE4.1.

---

## Comment 54

> Username: Lastique  
> Created_at: 2026-01-06 13:04:45 UTC  
> Url: https://github.com/boostorg/uuid/pull/186#issuecomment-3714627180  

> There are no AVX code paths in `to_chars`, although I see there is one in `from_chars`. All that time I assumed we were talking about AVX2, and your tables say AVX2. Odd.  
  
The AVX code path avoids `vpblendvb` instruction. Initially I applied the workaround only to AVX2, but later fixed it to apply to AVX as well. The performance tables were consistently reflecting the AVX2 code paths all along.  
  
> I want to optimize the case where the user uses `/arch:SSE4.2`. I don't see how this can be construed as the user not enabling SSE4.1, because SSE4.2 implies SSE4.1.  
  
Well, since the compiler doesn't define any macros to detect that, I don't see what we can do about that.

---
