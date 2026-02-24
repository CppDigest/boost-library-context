# #184 Add SIMD implementation of `to_chars` [Merged]

> Username: Lastique  
> Created at: 2025-12-16 16:10:36 UTC  
> Updated at: 2025-12-28 11:22:56 UTC  
> Merged at: 2025-12-28 10:09:20 UTC  
> Closed at: 2025-12-28 10:09:20 UTC  
> Url: https://github.com/boostorg/uuid/pull/184  

Moved the generic `to_chars` implementation to a separate header and made `to_chars.hpp` select the implementation based on the enabled SIMD ISA extensions. Added an x86 implementation leveraging SSSE3 and later vector extensions. Added detection of the said extensions to `config.hpp`. Updated tests and CI to test `to_chars` with various ISA extensions. Updated docs with the new macros and a release note.  
  
The performance effect on Intel Golden Cove (Core i7-12700K), gcc 13.3, in millions of `to_chars()` calls per second with a 16-byte aligned output buffer:  
  
```  
Char     | Generic | SSE4.1           | AVX2             | AVX-512  
=========+=========+==================+==================+=================  
char     | 203.190 | 1059.322 (5.21x) | 1053.352 (5.18x) | 1058.089 (5.21x)  
char16_t | 184.003 |  848.356 (4.61x) | 1009.489 (5.49x) | 1011.122 (5.50x)  
char32_t | 202.425 |  484.801 (2.39x) |  676.338 (3.34x) |  462.770 (2.29x)  
```  
  
The core of the SIMD implementation is using 128-bit vectors, larger vectors are only used to convert to the target character types. This means that for 1-byte character types all vector implementations are basically the same (barring the added ISA flexibility added by AVX) and for 2-byte character types AVX2 and AVX-512 are basically the same.  
  
For 4-byte character types, AVX-512 showed worse performance than SSE4.1 and AVX2 on the test system. It isn't clear why that is, but it is possible that the CPU throttles 512-bit instructions so much that the performance drops below a 256-bit equivalent. Perhaps, there are just not enough 512-bit instructions for the CPU to power up the full 512-bit pipeline. Therefore, the AVX-512 code path for 4-byte character types is currently disabled and the AVX2 path is used instead (which makes AVX2 and AVX-512 versions basically equivalent). The AVX-512 path can be enabled again if new CPU microarchitectures appear that will benefit from it.  
  
Higher alignment values of the output buffer were also tested, but they did not meaningfully improve performance.  
  
Here is the test code that was used for benchmarking:  
  
[uuid_to_chars_perftest.cpp](https://github.com/user-attachments/files/24195334/uuid_to_chars_perftest.cpp)  
  
Compile and run with:  
  
```  
g++ -O3 -march=x86-64 -std=gnu++17 -I. -o uuid_to_chars_perftest_generic uuid_to_chars_perftest.cpp  
g++ -O3 -march=nehalem -std=gnu++17 -I. -o uuid_to_chars_perftest_sse41 uuid_to_chars_perftest.cpp  
g++ -O3 -march=haswell -std=gnu++17 -I. -o uuid_to_chars_perftest_avx2 uuid_to_chars_perftest.cpp  
g++ -O3 -march=icelake-client -std=gnu++17 -I. -o uuid_to_chars_perftest_avx512 uuid_to_chars_perftest.cpp  
  
taskset --cpu-list 6 ./uuid_to_chars_perftest_generic  
taskset --cpu-list 6 ./uuid_to_chars_perftest_sse41  
taskset --cpu-list 6 ./uuid_to_chars_perftest_avx2  
taskset --cpu-list 6 ./uuid_to_chars_perftest_avx512  
```  
  
`taskset` to used to bind test execution to a single CPU core to improve stability of the results. You can choose the core number accordingly to your system.  
  
Full results:  
  
```  
Generic:  
char, alignment: 1: 162970 us, 122.721973 Mcalls/sec  
char, alignment: 16: 98430 us, 203.190084 Mcalls/sec  
char, alignment: 32: 97829 us, 204.438357 Mcalls/sec  
char, alignment: 64: 97927 us, 204.233766 Mcalls/sec  
char16_t, alignment: 2: 109485 us, 182.673426 Mcalls/sec  
char16_t, alignment: 16: 108694 us, 184.002797 Mcalls/sec  
char16_t, alignment: 32: 108696 us, 183.999411 Mcalls/sec  
char16_t, alignment: 64: 108845 us, 183.747531 Mcalls/sec  
char32_t, alignment: 4: 100438 us, 199.127820 Mcalls/sec  
char32_t, alignment: 16: 98802 us, 202.425052 Mcalls/sec  
char32_t, alignment: 32: 100827 us, 198.359566 Mcalls/sec  
char32_t, alignment: 64: 100227 us, 199.547028 Mcalls/sec  
  
SSE4.1:  
char, alignment: 1: 81858 us, 244.325539 Mcalls/sec  
char, alignment: 16: 18880 us, 1059.322034 Mcalls/sec  
char, alignment: 32: 17084 us, 1170.686022 Mcalls/sec  
char, alignment: 64: 17080 us, 1170.960187 Mcalls/sec  
char16_t, alignment: 2: 23579 us, 848.212392 Mcalls/sec  
char16_t, alignment: 16: 23575 us, 848.356310 Mcalls/sec  
char16_t, alignment: 32: 23607 us, 847.206337 Mcalls/sec  
char16_t, alignment: 64: 23582 us, 848.104486 Mcalls/sec  
char32_t, alignment: 4: 41269 us, 484.625264 Mcalls/sec  
char32_t, alignment: 16: 41254 us, 484.801474 Mcalls/sec  
char32_t, alignment: 32: 41276 us, 484.543076 Mcalls/sec  
char32_t, alignment: 64: 41255 us, 484.789722 Mcalls/sec  
  
AVX2:  
char, alignment: 1: 82345 us, 242.880563 Mcalls/sec  
char, alignment: 16: 18987 us, 1053.352294 Mcalls/sec  
char, alignment: 32: 17246 us, 1159.689203 Mcalls/sec  
char, alignment: 64: 17120 us, 1168.224299 Mcalls/sec  
char16_t, alignment: 2: 19803 us, 1009.947988 Mcalls/sec  
char16_t, alignment: 16: 19812 us, 1009.489198 Mcalls/sec  
char16_t, alignment: 32: 19768 us, 1011.736139 Mcalls/sec  
char16_t, alignment: 64: 19792 us, 1010.509297 Mcalls/sec  
char32_t, alignment: 4: 31409 us, 636.760164 Mcalls/sec  
char32_t, alignment: 16: 29571 us, 676.338304 Mcalls/sec  
char32_t, alignment: 32: 29593 us, 675.835502 Mcalls/sec  
char32_t, alignment: 64: 29571 us, 676.338304 Mcalls/sec  
  
Enabled AVX-512:  
char, alignment: 1: 81708 us, 244.774074 Mcalls/sec  
char, alignment: 16: 18902 us, 1058.089091 Mcalls/sec  
char, alignment: 32: 17122 us, 1168.087840 Mcalls/sec  
char, alignment: 64: 17147 us, 1166.384790 Mcalls/sec  
char16_t, alignment: 2: 19732 us, 1013.581999 Mcalls/sec  
char16_t, alignment: 16: 19780 us, 1011.122346 Mcalls/sec  
char16_t, alignment: 32: 19664 us, 1017.087063 Mcalls/sec  
char16_t, alignment: 64: 19664 us, 1017.087063 Mcalls/sec  
char32_t, alignment: 4: 43198 us, 462.984397 Mcalls/sec  
char32_t, alignment: 16: 43218 us, 462.770142 Mcalls/sec  
char32_t, alignment: 32: 43194 us, 463.027272 Mcalls/sec  
char32_t, alignment: 64: 43200 us, 462.962963 Mcalls/sec  
```  
  
In order to produce the AVX-512 results, the AVX10.1 branch in the code needs to be enabled. Otherwise, AVX-512 would be basically the same as AVX2.

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-12-17 10:14:39 UTC  
> Url: https://github.com/boostorg/uuid/pull/184#issuecomment-3664644516  

Very nice, thanks.  
  
When removing macos-13, you should add macos-26.  
  
We'll need to figure out constexpr at some point though.

---

## Comment 2

> Username: Lastique  
> Created_at: 2025-12-17 10:49:55 UTC  
> Url: https://github.com/boostorg/uuid/pull/184#issuecomment-3664785951  

> When removing macos-13, you should add macos-26.  
  
You mean macos-16? I don't see it available [here](https://github.com/actions/runner-images). Are you sure it exists?

---

## Comment 3

> Username: Lastique  
> Created_at: 2025-12-17 10:51:56 UTC  
> Url: https://github.com/boostorg/uuid/pull/184#issuecomment-3664793569  

> We'll need to figure out constexpr at some point though.  
  
Well, constexpr will have to use the generic version as vector intrinsics are not constexpr-friendly. But that would have to be a separate work.

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-12-17 10:58:11 UTC  
> Url: https://github.com/boostorg/uuid/pull/184#issuecomment-3664817004  

No, I mean macos-26. They changed their version numbering. E.g. https://github.com/boostorg/core/commit/5bea3b85e627bc43e153ec1fea3922fe972b0509.

---

## Comment 5

> Username: Lastique  
> Created_at: 2025-12-17 11:15:44 UTC  
> Url: https://github.com/boostorg/uuid/pull/184#issuecomment-3664878339  

> No, I mean macos-26. They changed their version numbering. E.g. [boostorg/core@5bea3b8](https://github.com/boostorg/core/commit/5bea3b85e627bc43e153ec1fea3922fe972b0509).  
  
Ok, done.

---

## Comment 6

> Username: Lastique  
> Created_at: 2025-12-17 11:50:54 UTC  
> Updated_at: 2025-12-17 12:54:10 UTC  
> Url: https://github.com/boostorg/uuid/pull/184#issuecomment-3664992866  

I should note that the SIMD code relies on that zero-extending character codes from 8 bits to larger character types is correct. This is true for Unicode-based systems, but I have no experience with weird ones like EBCDIC.  
  
Also, I'm currently using `char` constants to define hexadecimal and dash characters. Which will probably produce incorrect results if `char` is e.g. EBCDIC and `charN_t` are Unicode (and I have no idea what would `wchar_t` be on such a system). I could probably fix that by adding different vector constants.

---

## Comment 7

> Username: pdimov  
> Created_at: 2025-12-17 13:51:15 UTC  
> Url: https://github.com/boostorg/uuid/pull/184#issuecomment-3665450158  

Zero-extending `char` to `char16_t` or `char32_t` is not correct when `char` is EBCDIC. I think that it's also not correct for `wchar_t` in this case. On EBCDIC systems, `wchar_t` is something called "wide EBCDIC".  
  
There's no way to detect EBCDIC at preprocessor time though.

---

## Comment 8

> Username: pdimov  
> Created_at: 2025-12-17 14:11:38 UTC  
> Url: https://github.com/boostorg/uuid/pull/184#issuecomment-3665543988  

Zero-extending u8"0123456789abcdef" should work for char16_t and char32_t though. The type of the u8 literal changes from char* to char8_t* in C++17 (or was it 20) but if you load it as void* it shouldn't matter.  
  
Wide EBCDIC is a weird locale-dependent "multibyte" (two bytes into one word) encoding and char -> wchar_t might work, but I don't know for sure and we have no way to test it.

---

## Comment 9

> Username: Lastique  
> Created_at: 2025-12-17 14:35:04 UTC  
> Url: https://github.com/boostorg/uuid/pull/184#issuecomment-3665640836  

I've updated the code to use separate constants when `char` and `wchar_t` are incompatible with ASCII. This won't have impact on performance.

---

## Comment 10

> Username: Lastique  
> Created_at: 2025-12-24 21:35:39 UTC  
> Url: https://github.com/boostorg/uuid/pull/184#issuecomment-3690524590  

BTW, the requirement that '-' is less than any hexadecimal character codes can be lifted, if needed. One way to do it is to subtract '-' from `mm_char_table` elements and replace `_mm_max_epu8` with `_mm_add_epi8`. There would need to be an extra `_mm_add_epi8` for `mm_chars3`, which would incur a small performance hit.  
  
I don't think there's any real world character encoding where that requirement doesn't hold, so I prefer to keep the current version. But if you want, I can apply the above change.

---

## Comment 11

> Username: pdimov  
> Created_at: 2025-12-25 00:51:29 UTC  
> Url: https://github.com/boostorg/uuid/pull/184#issuecomment-3690665774  

I don't think there's any need. The only non-ASCII encoding in use today is EBCDIC, for which '-' (0x60) is lower than 'a' (0x81), 'A' (0xC1) and '0' (0xF0). But even if that weren't true, EBCDIC is only used by z/OS, which runs on OS/390 mainframes, and those aren't Intel and don't have SSE2 et al.

---

## Comment 12

> Username: Lastique  
> Created_at: 2025-12-25 02:58:07 UTC  
> Url: https://github.com/boostorg/uuid/pull/184#issuecomment-3690781809  

Ok. Do you have any further comments or requests? Otherwise, this is ready to be merged.

---

## Comment 13

> Username: pdimov  
> Created_at: 2025-12-25 21:35:48 UTC  
> Url: https://github.com/boostorg/uuid/pull/184#issuecomment-3691761152  

I wanted to finish the constexpr-ification first, sorry; it intersects with to_chars a bit.

---

## Comment 14

> Username: pdimov  
> Created_at: 2025-12-27 17:05:41 UTC  
> Url: https://github.com/boostorg/uuid/pull/184#issuecomment-3694098666  

I think I'm done with the constexpr-ification; let me know if you need anything from me in order to proceed.

---

## Comment 15

> Username: Lastique  
> Created_at: 2025-12-27 18:19:18 UTC  
> Url: https://github.com/boostorg/uuid/pull/184#issuecomment-3694139539  

Thanks. Rebased on top develop and renamed the added headers to ".hpp" (as the new headers are self-contained). This PR should be ready to merge.

---
