# #151 Add AVX-512/AVX10.1 implementation of `operator<`/`operator<=>` [Merged]

> Username: Lastique  
> Created at: 2024-04-24 14:04:11 UTC  
> Updated at: 2024-04-25 18:18:48 UTC  
> Merged at: 2024-04-25 17:14:55 UTC  
> Closed at: 2024-04-25 17:14:55 UTC  
> Url: https://github.com/boostorg/uuid/pull/151  

The new implementation offers about 20% speed improvement over the SSE2 version on i7 12700K, and about 30% over the `uint128_t`-based version.  
  
Also added GitHub CI jobs with AVX-512 enabled to test the new implementation.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-04-24 15:16:26 UTC  
> Url: https://github.com/boostorg/uuid/pull/151#issuecomment-2075193723  

Presuming that GHA runners have AVX-512 was certainly optimistic. :-)

---

## Comment 2

> Username: Lastique  
> Created_at: 2024-04-24 15:34:50 UTC  
> Url: https://github.com/boostorg/uuid/pull/151#issuecomment-2075233439  

> Presuming that GHA runners have AVX-512 was certainly optimistic. :-)  
  
It was worth a try.  
  
I've removed the commit with CI changes.

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-04-24 15:37:47 UTC  
> Url: https://github.com/boostorg/uuid/pull/151#issuecomment-2075241776  

https://github.com/actions/runner/issues/1069

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-04-24 15:38:09 UTC  
> Url: https://github.com/boostorg/uuid/pull/151#issuecomment-2075243514  

https://github.com/actions/runner-images/issues/3389

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-04-24 17:48:03 UTC  
> Url: https://github.com/boostorg/uuid/pull/151#issuecomment-2075508153  

We're Asciidoc now thanks to @cmazakas (https://github.com/boostorg/uuid/pull/152).

---

## Comment 6

> Username: Lastique  
> Created_at: 2024-04-24 19:41:44 UTC  
> Url: https://github.com/boostorg/uuid/pull/151#issuecomment-2075709512  

Updated to Asciidoc.

---

## Comment 7

> Username: pdimov  
> Created_at: 2024-04-25 17:12:31 UTC  
> Url: https://github.com/boostorg/uuid/pull/151#issuecomment-2077773197  

Having this without CI coverage makes me a bit uneasy, although I suppose we'll get an AVX-512 runner every now and then on either GHA or Drone, depending on the phase of the moon.  
  
I can't even test it locally because I don't have AVX-512. :-)

---

## Comment 8

> Username: Lastique  
> Created_at: 2024-04-25 17:26:34 UTC  
> Url: https://github.com/boostorg/uuid/pull/151#issuecomment-2077797260  

FWIW, you can run the tests locally under [Intel SDE](https://software.intel.com/en-us/articles/intel-software-development-emulator).

---

## Comment 9

> Username: pdimov  
> Created_at: 2024-04-25 17:39:21 UTC  
> Url: https://github.com/boostorg/uuid/pull/151#issuecomment-2077817357  

Oh, it occurs to me that even if we get an AVX-512 runner by chance, we'll still not test AVX-512 because we won't be enabling it via `instruction-set`.  
  
Would be nice if we can figure something out.

---

## Comment 10

> Username: Lastique  
> Created_at: 2024-04-25 17:43:06 UTC  
> Url: https://github.com/boostorg/uuid/pull/151#issuecomment-2077823298  

We could try checking `lscpu | fgrep avx512f` on Linux machines before building/testing. I don't know an easy way to check AVX-512 presence on Windows, other than to build a program that executes `cpuid`.  
  
I'll see if I can do something on Linux.

---

## Comment 11

> Username: pdimov  
> Created_at: 2024-04-25 18:00:08 UTC  
> Url: https://github.com/boostorg/uuid/pull/151#issuecomment-2077851482  

Using `-march=native` will be much easier. Unfortunately `b2` doesn't support that natively. (But then again it doesn't support `instruction-set` at all for MSVC.)

---

## Comment 12

> Username: pdimov  
> Created_at: 2024-04-25 18:03:55 UTC  
> Url: https://github.com/boostorg/uuid/pull/151#issuecomment-2077858492  

Unrelated, do you by any chance know why `test_time_generator_v1_2` (the one using `std::atomic`) would hang on GCC 13 on GHA? Does something like that happen in the Boost.Atomic tests? (On Drone, only the 32 bit UBSAN run would hang.)

---

## Comment 13

> Username: Lastique  
> Created_at: 2024-04-25 18:17:11 UTC  
> Url: https://github.com/boostorg/uuid/pull/151#issuecomment-2077885475  

> Unrelated, do you by any chance know why `test_time_generator_v1_2` (the one using `std::atomic`) would hang on GCC 13 on GHA?  
  
No.  
  
I remember there was a similar problem in Boost.Core, but there it was with CMake jobs. I "solved" it by switching from Makefiles to Ninja.  
  
The last few Boost.UUID CI runs were successful though. Does it still happen for you?  
  
> Does something like that happen in the Boost.Atomic tests?  
  
Not last time I checked. Though I'm not using `std::atomic` there. :)

---

## Comment 14

> Username: pdimov  
> Created_at: 2024-04-25 18:18:46 UTC  
> Url: https://github.com/boostorg/uuid/pull/151#issuecomment-2077889402  

> The last few Boost.UUID CI runs were successful though. Does it still happen for you?  
  
That's because I disabled gcc-13 in the Jamfile. :-)

---
