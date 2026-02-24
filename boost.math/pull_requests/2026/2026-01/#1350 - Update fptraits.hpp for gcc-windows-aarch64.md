# #1350 Update fptraits.hpp for gcc/windows/aarch64. [Merged]

> Username: jzmaddock  
> Created at: 2026-01-13 12:09:37 UTC  
> Updated at: 2026-01-27 13:54:54 UTC  
> Merged at: 2026-01-24 14:04:17 UTC  
> Closed at: 2026-01-24 14:04:17 UTC  
> Url: https://github.com/boostorg/math/pull/1350  

Also adds better error checking.  
Fixes #1348

---

## Review 1 [Commented]

> Username: mborland  
> Created_at: 2026-01-13 13:19:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1350#pullrequestreview-3655762720  

📁 include/boost/math/special_functions/detail/fp_traits.hpp

```diff
 364 | 
 365 |+ static_assert(LDBL_MANT_DIG == 113, "Oops, assumption that long double is a 128-bit quantity is incorrect!!");
 366 |+ 
```

> Username: mborland  
> Created_at: 2026-01-13 13:19:38 UTC  
> Updated_at: 2026-01-13 13:19:52 UTC  
> Url: https://github.com/boostorg/math/pull/1350#discussion_r2686405160  

In the PowerPC case should we check both 106 and 113 for `LDBL_MANT_DIG`? I assume the original code is old enough that 106 was the only option at the time.

> Username: jzmaddock  
> Created_at: 2026-01-16 12:39:09 UTC  
> Url: https://github.com/boostorg/math/pull/1350#discussion_r2698367870  

Not in the same branch, everything has to go to the correct #if branch otherwise we don't get the correct bits.


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2026-01-16 12:48:52 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3759882202  

@mborland I can't seem to get CUDA/SYCL correct: am I correct in thinking that long double is 64 bit on the device and 80-bit on the host?  If so is there a good way to configure that?

---

## Comment 3

> Username: mborland  
> Created_at: 2026-01-16 12:58:26 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3759919701  

> @mborland I can't seem to get CUDA/SYCL correct: am I correct in thinking that long double is 64 bit on the device and 80-bit on the host?    
  
That is correct.  
  
> If so is there a good way to configure that?  
  
I think we'd need to have one set of functions marked __host__ and one set marked __device__ and then let the compiler pick the right one. I'm out of town for the long weekend (MLK day), but I can add those on Tuesday if you'd like.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2026-01-18 19:48:25 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3765672016  

> I think we'd need to have one set of functions marked host and one set marked device and then let the compiler pick the right one. I'm out of town for the long weekend (MLK day), but I can add those on Tuesday if you'd like.  
  
Hmm, I suspect that since the functions aren't dependent on any template parameter, the static_assert will be triggered irrespective of whether the function is called or not.

---

## Comment 5

> Username: mborland  
> Created_at: 2026-01-20 16:40:18 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3773874488  

> > I think we'd need to have one set of functions marked host and one set marked device and then let the compiler pick the right one. I'm out of town for the long weekend (MLK day), but I can add those on Tuesday if you'd like.  
>   
> Hmm, I suspect that since the functions aren't dependent on any template parameter, the static_assert will be triggered irrespective of whether the function is called or not.  
  
I guess I was thinking about moving the `static_assert` into the getter and setter functions like:  
  
```  
__host__ get_bits() { static_assert(LDBL_MANT_DIG == 64); 80 bit impl; }  
__device__ get_bits() { static_assert(LDBL_MANT_DIG == 53); 64 bit impl; }   
  
__host__ set_bits() { static_assert(LDBL_MANT_DIG == 64); 80 bit impl; }  
__device__ set_bits() { static_assert(LDBL_MANT_DIG == 53); 64 bit impl; }   
```  
  
The rub is then the tag is wrong on device?

---

## Comment 6

> Username: mborland  
> Created_at: 2026-01-20 17:46:47 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3774173117  

Actually, even easier is removing the check for GPU platforms. I was wrong and `long double` is [entirely unsupported and warns if used.](https://forums.developer.nvidia.com/t/support-for-long-double-data-type-in-gpu-code/313337/4). There are also no GPU markers on any of the long double functions so they will error if called anyway.

---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2026-01-20 19:32:21 UTC  
> Updated_at: 2026-01-24 14:05:09 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3774592355  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1350?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.29%. Comparing base ([`1d670c8`](https://app.codecov.io/gh/boostorg/math/commit/1d670c85b4918d1e703f2fda9abe24d5d8bf60d5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`935e699`](https://app.codecov.io/gh/boostorg/math/commit/935e699a0cedb5c1a7876a7d2e4c9d5166692893?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 7 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1350/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1350?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1350   +/-   ##  
========================================  
  Coverage    95.29%   95.29%             
========================================  
  Files          814      814             
  Lines        67422    67422             
========================================  
  Hits         64249    64249             
  Misses        3173     3173             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1350?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [.../boost/math/special\_functions/detail/fp\_traits.hpp](https://app.codecov.io/gh/boostorg/math/pull/1350?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fspecial_functions%2Fdetail%2Ffp_traits.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3NwZWNpYWxfZnVuY3Rpb25zL2RldGFpbC9mcF90cmFpdHMuaHBw) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1350?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1350?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1d670c8...935e699](https://app.codecov.io/gh/boostorg/math/pull/1350?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2026-01-24 12:53:16 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3794584952  

Looks like this one is good to go @mborland ?

---

## Comment 9

> Username: mborland  
> Created_at: 2026-01-24 14:04:12 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3794702884  

> Looks like this one is good to go @mborland ?  
  
Yes. In it goes.

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2026-01-24 19:01:50 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3795363212  

Oh geez, literally 5 minutes after this merge, my builds on `arm-none-eabi` for 32-bit microcontrollers broke.  
  
```  
In file included from ../../boost-root/boost/math/special_functions/fpclassify.hpp:21,  
                 from ../../boost-root/boost/math/special_functions/cbrt.hpp:23,  
                 from ./src/app/benchmark/app_benchmark_boost_math_cbrt_tgamma.cpp:40:  
../../boost-root/boost/math/special_functions/detail/fp_traits.hpp:441:29: error: static assertion failed: Oops, assumption that long double is a 128-bit quantity is incorrect!!  
  441 | static_assert(LDBL_MANT_DIG == 113, "Oops, assumption that long double is a 128-bit quantity is incorrect!!");  
      |                             ^  
../../boost-root/boost/math/special_functions/detail/fp_traits.hpp:441:29: note: the comparison reduces to '(53 == 113)'  
In file included from ../../boost-root/boost/math/special_functions/sign.hpp:21,  
                 from ../../boost-root/boost/math/tools/roots.hpp:20,  
                 from ../../boost-root/boost/math/special_functions/bessel.hpp:22,  
                 from ./src/app/benchmark/app_benchmark_boost_math_cyl_bessel_j.cpp:71:  
../../boost-root/boost/math/special_functions/detail/fp_traits.hpp:441:29: error: static assertion failed: Oops, assumption that long double is a 128-bit quantity is incorrect!!  
  441 | static_assert(LDBL_MANT_DIG == 113, "Oops, assumption that long double is a 128-bit quantity is incorrect!!");  
      |                             ^  
../../boost-root/boost/math/special_functions/detail/fp_traits.hpp:441:29: note: the comparison reduces to '(53 == 113)'  
In file included from ../../boost-root/boost/math/special_functions/fpclassify.hpp:21,  
                 from ../../boost-root/boost/math/special_functions/cbrt.hpp:23,  
                 from ./src/app/benchmark/app_benchmark_boost_multiprecision_cbrt.cpp:43:  
../../boost-root/boost/math/special_functions/detail/fp_traits.hpp:441:29: error: static assertion failed: Oops, assumption that long double is a 128-bit quantity is incorrect!!  
  441 | static_assert(LDBL_MANT_DIG == 113, "Oops, assumption that long double is a 128-bit quantity is incorrect!!");  
      |                             ^  
../../boost-root/boost/math/special_functions/detail/fp_traits.hpp:441:29: note: the comparison reduces to '(53 == 113)'  
```

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2026-01-24 19:06:23 UTC  
> Updated_at: 2026-01-24 19:07:05 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3795367554  

I could add some lines around line 273 to pick up `arm-none-eabi`-GCC. But, noting that this toolchain is not one of the ones officially supported in `Boost.Math`, I could also leave it. but it would be a shame if I couldn't compile math any more on ARM `aarch32`. I need to check if there is a good fix for this one

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2026-01-24 19:14:43 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3795374361  

Around lines 273, I could add  
  
```  
(defined(__arm__) || defined(__thumb__))  
```  
  
which will _NOT_ pick up `__aarch64__`. THen at least I get my Cortex(R) controllers?

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2026-01-25 17:55:51 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3797017272  

Thanks for this @ckormanyos , just thinking out loud here, I wonder if it's possible to change the default branch to only check long double sizes if that function is actually used (we would need to make it a template somehow)?  Otherwise we risk this issue coming up again and again as new platforms get used.

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2026-01-25 21:32:06 UTC  
> Updated_at: 2026-01-25 21:32:52 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3797266939  

> I wonder if it's possible to change the default branch to only check long double sizes if that function is actually used (we would need to make it a template somehow)? Otherwise we risk this issue coming up again and again as new platforms get used.  
  
You know @jzmaddock, I still struggled with this one. I got it working locally, but still had to disable some tests in my embedded CI/CD.  
  
So I haven't fully understood the testing logic yet. At this moment, I think we have something that passes CI/CD on Boost.Math. And I can work successfully locally with it.  
  
But indeed, I think the logic could and probably will be refined. I just got in a super-hurry yesterday and put in the minimal PP-stuff I needed for my local builds. It can surely be improved down the road.

---

## Comment 15

> Username: mborland  
> Created_at: 2026-01-26 13:52:31 UTC  
> Updated_at: 2026-01-26 13:57:50 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3799730007  

I'm wondering if we can't remove much of the use? For instance `fpclassify`, `isnan` etc. were standardized in C++11. These `fp_traits` seem to be the predecessor to that, which can now be safely removed since we require C++14.

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2026-01-26 15:56:00 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3800336901  

>I'm wondering if we can't remove much of the use? For instance fpclassify, isnan etc. were standardized in C++11. These fp_traits seem to be the predecessor to that, which can now be safely removed since we require C++14.  
  
Perhaps, but in general they're pretty efficient and portable.  They also correctly detect NaN's in situations where some `std::isnan` implementations fail to do so.

---

## Comment 17

> Username: mborland  
> Created_at: 2026-01-26 16:35:00 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3800530985  

This broke the PPC64LE QEMU runners from Boost.CI:  
  
```  
In file included from ./boost/math/special_functions/fpclassify.hpp:21,  
                 from ./boost/math/special_functions/next.hpp:20,  
                 from libs/decimal/test/test_legendre.cpp:31:  
./boost/math/special_functions/detail/fp_traits.hpp:367:29: error: static assertion failed: Oops, assumption that long double is a 128-bit quantity is incorrect!!  
  367 | static_assert(LDBL_MANT_DIG == 113, "Oops, assumption that long double is a 128-bit quantity is incorrect!!");  
      |                             ^  
./boost/math/special_functions/detail/fp_traits.hpp:367:29: note: the comparison reduces to '(53 == 113)'  
  
    "g++"   -std=c++14 -fvisibility-inlines-hidden -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -Werror -fvisibility=hidden -Wall -Wconversion -Wextra -Wfloat-equal -Wold-style-cast -Wshadow -Wsign-conversion -Wundef -Wuseless-cast -DBOOST_ALL_NO_LIB=1 -DBOOST_DECIMAL_QEMU_TEST -DNDEBUG   -I"." -I"/boost/libs/decimal/include"  -c -o "bin.v2/libs/decimal/test/test_legendre.test/gcc-14/release/power_64/cxxstd-14-iso/link-static/threading-multi/visibility-hidden/test_legendre.o" "libs/decimal/test/test_legendre.cpp"  
```

---

## Comment 18

> Username: ckormanyos  
> Created_at: 2026-01-26 16:59:15 UTC  
> Updated_at: 2026-01-26 17:00:05 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3800647409  

> This broke the PPC64LE QEMU runners from Boost.CI  
  
Hi @mborland, good catch.  
  
Ugghhh what exactly broke the 64-bit Power PC QEMU runners?  
  
Were my very recent changes for `__arm__` the culprit, or do you mean this entire PR?

---

## Comment 19

> Username: mborland  
> Created_at: 2026-01-26 17:04:13 UTC  
> Updated_at: 2026-01-26 17:05:23 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3800670826  

The entire PR, since you didn't touch this path. I'm thinking it may be time to roll this back. There's going to be lots of other platforms we don't account for I'm sure. I'll defer to @jzmaddock

---

## Comment 20

> Username: ckormanyos  
> Created_at: 2026-01-26 17:23:55 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3800761852  

> I'm thinking it may be time to roll this back. There's going to be lots of other platforms we don't account for I'm sure. I'll defer, ...  
  
That is a good consideration Matt (@mborland). It's one of those things, ... do you roll back or press on?  
  
I have Boost.Math running on embedded `__arm__`, `__riscv__`, and a few others in addition to the classic host workstations like PCs and LINUX.  
  
For the record, I can _live_ with this PR.  
  
So if we get the failed runners on PPC-64 working, that would be sufficient for me at the moment.  
  
Cc: @jzmaddock

---

## Comment 21

> Username: jzmaddock  
> Created_at: 2026-01-27 12:28:04 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3804951852  

>This broke the PPC64LE QEMU runners from Boost.CI:  
  
Where can I see these running?  
  
I'm interested in the output from math_info.cpp on these platforms if possible?  
  
Meanwhile, see if https://github.com/boostorg/math/pull/1354 helps.

---

## Comment 22

> Username: mborland  
> Created_at: 2026-01-27 13:54:54 UTC  
> Url: https://github.com/boostorg/math/pull/1350#issuecomment-3805347831  

> > This broke the PPC64LE QEMU runners from Boost.CI:  
>   
> Where can I see these running?  
>   
  
Here is an example: https://github.com/cppalliance/decimal/actions/runs/21370721961/job/61514255669  
  
> I'm interested in the output from math_info.cpp on these platforms if possible?  
>   
  
I'll have to see if I can get these containers running locally to give it a shot. It's pulling in math from develop in the CI.  
  
> Meanwhile, see if #1354 helps.

---
