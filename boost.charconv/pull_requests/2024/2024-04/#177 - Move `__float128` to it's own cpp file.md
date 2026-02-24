# #177 Move `__float128` to it's own cpp file [Merged]

> Username: mborland  
> Created at: 2024-04-08 07:39:01 UTC  
> Updated at: 2024-04-11 07:12:55 UTC  
> Merged at: 2024-04-11 07:12:51 UTC  
> Closed at: 2024-04-11 07:12:51 UTC  
> Url: https://github.com/boostorg/charconv/pull/177  

Closes: #176   
  
@pdimov What do you think of this? I think the number of people who want quadmath support is smaller than the number of people using CMake, which makes it really easy to hit the linked issue.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-04-08 07:50:56 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2042087018  

An automated preview of the documentation is available at [https://177.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://177.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-04-08 09:47:16 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2042318505  

The problem with opt-in is that many people don't build Boost themselves, they rely on prebuilt binaries from e.g. the system package manager, and if the prebuilt one hasn't opted in, it won't have quadmath support.  
  
I suppose this isn't that problematic, but it's something to be aware of.  
  
Why does autodetection fail in this case?

---

## Comment 3

> Username: mborland  
> Created_at: 2024-04-08 09:52:31 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2042328691  

> Why does autodetection fail in this case?  
  
I can check for three things:  
  
1) Does the system have `__float128`  
2) Is `<quadmath.h>` installed  
3) Is the user compiling with GNU extensions.  
  
On the linked issue all of these are met but Alpine uses the musl standard library, and has quadmath installed somewhere unexpected so `target_link_libraries(... quadmath)` also fails.

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-04-08 10:00:38 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2042344349  

But you can do a configure check, and reliably set a macro if it works.

---

## Comment 5

> Username: anarthal  
> Created_at: 2024-04-08 10:42:09 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2042421599  

I'm with @pdimov. Why don't you just use the config check you have in B2 and CMake to define a macro? Something along the line  
  
```  
[ check-target-builds ../config//has_float128 "GCC libquadmath and __float128 support" : <library>"quadmath" <define>BOOST_CHARCONV_HAS_QUADMATH ]  
```  
  
(and the equivalent in CMake).

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2024-04-09 10:30:58 UTC  
> Updated_at: 2024-04-09 10:43:35 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2044671819  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/177?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.29%. Comparing base [(`d1236ab`)](https://app.codecov.io/gh/boostorg/charconv/commit/d1236abb2e003825d5ccda4cb628aadde9fd92c5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`147b1f4`)](https://app.codecov.io/gh/boostorg/charconv/pull/177?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/177/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/177?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #177   +/-   ##  
========================================  
  Coverage    93.29%   93.29%             
========================================  
  Files           65       65             
  Lines         8571     8571             
========================================  
  Hits          7996     7996             
  Misses         575      575             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/charconv/pull/177?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/detail/compute\_float80.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/177?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fcompute_float80.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvY29tcHV0ZV9mbG9hdDgwLmhwcA==) | `91.30% <ø> (ø)` | |  
| [include/boost/charconv/detail/emulated128.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/177?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Femulated128.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZW11bGF0ZWQxMjguaHBw) | `100.00% <ø> (ø)` | |  
| [...nclude/boost/charconv/detail/fallback\_routines.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/177?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Ffallback_routines.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZmFsbGJhY2tfcm91dGluZXMuaHBw) | `84.61% <ø> (ø)` | |  
| [...lude/boost/charconv/detail/ryu/ryu\_generic\_128.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/177?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fryu%2Fryu_generic_128.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcnl1L3J5dV9nZW5lcmljXzEyOC5ocHA=) | `87.86% <ø> (ø)` | |  
| [include/boost/charconv/from\_chars.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/177?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Ffrom_chars.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9mcm9tX2NoYXJzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/charconv/to\_chars.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/177?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fto_chars.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi90b19jaGFycy5ocHA=) | `100.00% <ø> (ø)` | |  
| [src/from\_chars.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/177?src=pr&el=tree&filepath=src%2Ffrom_chars.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Zyb21fY2hhcnMuY3Bw) | `90.00% <ø> (ø)` | |  
| [src/to\_chars.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/177?src=pr&el=tree&filepath=src%2Fto_chars.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzLmNwcA==) | `93.44% <ø> (ø)` | |  
| [src/to\_chars\_float\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/177?src=pr&el=tree&filepath=src%2Fto_chars_float_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzX2Zsb2F0X2ltcGwuaHBw) | `80.47% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/177?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/177?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d1236ab...147b1f4](https://app.codecov.io/gh/boostorg/charconv/pull/177?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 7

> Username: mborland  
> Created_at: 2024-04-10 06:37:11 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2046633832  

@pdimov When you get a chance can you please double check me on this. Changes are:  
  
1) Cmake and B2 now define a macro `BOOST_CHARCONV_BUILD_FLOAT128_SUPPORT` if the config test passes  
2) Everything related to implementation of `__float128` and `std::float128_t` are now in a header and source file in the src/ directory.   
3) The only time `<quadmath.h>` or any implementation __float128 is included is if `BOOST_CHARCONV_BUILD_FLOAT128_SUPPORT` is defined. Otherwise it's all #ifed out. This includes the declarations in `to_chars.hpp` and `from_chars.hpp`

---

## Comment 8

> Username: pdimov  
> Created_at: 2024-04-10 07:57:14 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2046806052  

There's probably no need to repeat  
```  
  target_link_libraries(boost_charconv  
    PUBLIC  
      Boost::config  
      Boost::assert  
      Boost::core  
  )  
```  
in both quadmath branches; we can pull it out and just add `target_link_libraries(boost_charconv PUBLIC quadmath)` in the second one.

---

## Comment 9

> Username: pdimov  
> Created_at: 2024-04-10 08:15:10 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2046860209  

This looks like it's going to work, except (1) the actual `to_chars` and `from_chars` definitions in `to_chars.cpp` and `from_chars.cpp` haven't been `ifdef`-ed on the new macro and (2) the `limits` specialization is not going to work, because there's no way `limits<__float128>::max_chars` can be a constant if the specialization is only in a .cpp file. The specialization needs to remain in the header, with the condition adjusted appropriately.

---

## Comment 10

> Username: pdimov  
> Created_at: 2024-04-10 08:20:47 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2046882797  

`float128.cpp` seems to be unnecessary, as it doesn't contain anything.  
```  
/Applications/Xcode_14.2.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ranlib: file: bin.v2/libs/charconv/build/clang-darwin-14/release/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/libboost_charconv.a(float128.o) has no symbols  
```

---

## Comment 11

> Username: pdimov  
> Created_at: 2024-04-10 08:23:17 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2046887375  

I'm not sure I understand why CI passes even though the conditions in `to_chars.cpp` and `from_chars.cpp` haven't been adjusted; it's probably because we don't have a configuration where `quadmath.h` exists but `libquadmath` does not.  
  
The limits thing should also cause failures but doesn't; maybe we aren't testing it, or maybe I'm missing something.

---

## Comment 12

> Username: mborland  
> Created_at: 2024-04-10 08:23:58 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2046888694  

> `float128.cpp` seems to be unnecessary, as it doesn't contain anything.  
>   
> ```  
> /Applications/Xcode_14.2.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ranlib: file: bin.v2/libs/charconv/build/clang-darwin-14/release/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/libboost_charconv.a(float128.o) has no symbols  
> ```  
  
Won't it only have symbols if `__float128` is supported? I thought Intel Macs didn't support the type, and I know ARM is unsupported by `<quadmath.h>`.

---

## Comment 13

> Username: pdimov  
> Created_at: 2024-04-10 08:24:37 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2046890024  

Yes, we are only testing `limits` for the three standard floating point types:  
https://github.com/boostorg/charconv/blob/d1236abb2e003825d5ccda4cb628aadde9fd92c5/test/limits.cpp#L228-L230

---

## Comment 14

> Username: pdimov  
> Created_at: 2024-04-10 08:27:13 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2046894700  

> Won't it only have symbols if `__float128` is supported? I thought Intel Macs didn't support the type, and I know ARM is unsupported by `<quadmath.h>`.  
  
Yes, you're right.  
  
Although... since `float128_impl.hpp` is included in `to_chars.cpp` and `from_chars.cpp`, I'm not sure `float128.cpp` is needed for anything even when quadmath is supported.

---

## Comment 15

> Username: mborland  
> Created_at: 2024-04-10 08:30:37 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2046900778  

> Yes, we are only testing `limits` for the three standard floating point types:  
>   
> https://github.com/boostorg/charconv/blob/d1236abb2e003825d5ccda4cb628aadde9fd92c5/test/limits.cpp#L228-L230  
  
https://github.com/boostorg/charconv/blob/d1236abb2e003825d5ccda4cb628aadde9fd92c5/test/github_issue_152_float128.cpp#L67  
  
Although I think these tests are getting completely skipped because they used the macro. It was the workaround for the Cmake tests failing on quadmath a while back.

---

## Comment 16

> Username: mborland  
> Created_at: 2024-04-10 08:31:13 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2046901898  

> > Won't it only have symbols if `__float128` is supported? I thought Intel Macs didn't support the type, and I know ARM is unsupported by `<quadmath.h>`.  
>   
> Yes, you're right.  
>   
> Although... since `float128_impl.hpp` is included in `to_chars.cpp` and `from_chars.cpp`, I'm not sure `float128.cpp` is needed for anything even when quadmath is supported.  
  
I will get rid of it.

---

## Comment 17

> Username: pdimov  
> Created_at: 2024-04-10 08:34:00 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2046906745  

> Although I think these tests are getting completely skipped because they used the macro.  
  
Should probably be changed to use the new macro, and the tests in `limits.cpp` updated for the rest of the floating point types when supported.

---

## Comment 18

> Username: pdimov  
> Created_at: 2024-04-10 08:35:52 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2046910318  

I wonder whether `BOOST_CHARCONV_HAS_QUADMATH` isn't a better name for this macro, especially if the user needs to define it himself in case the library is used directly, without b2 or CMake.

---

## Comment 19

> Username: mborland  
> Created_at: 2024-04-10 08:41:43 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2046921118  

> I wonder whether `BOOST_CHARCONV_HAS_QUADMATH` isn't a better name for this macro, especially if the user needs to define it himself in case the library is used directly, without b2 or CMake.  
  
I'd agree with that. I'll add it to the build section of the docs too.

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2024-04-10 09:07:11 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2046971373  

An automated preview of the documentation is available at [https://177.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://177.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2024-04-10 10:05:56 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2047114026  

An automated preview of the documentation is available at [https://177.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://177.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 22

> Username: pdimov  
> Created_at: 2024-04-10 13:53:56 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2047615443  

GCC 13 fails the _Float16 limits tests on Drone.  
  
Looks like we need to update our CI - GHA is missing GCC 13, Clang 16, Clang 17, macos-13, Drone is missing Clang 16 and 17.

---

## Comment 23

> Username: pdimov  
> Created_at: 2024-04-10 17:13:01 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2048080918  

Although it occurs to me that (a) updating the CI and (b) adding the float16/float32 tests to limits.cpp can be done in separate branches/PRs, as they are off-topic here.

---

## Comment 24

> Username: mborland  
> Created_at: 2024-04-11 06:11:43 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2048989039  

> GCC 13 fails the _Float16 limits tests on Drone.  
>   
> Looks like we need to update our CI - GHA is missing GCC 13, Clang 16, Clang 17, macos-13, Drone is missing Clang 16 and 17.  
  
It's probably the exact same issue as: https://github.com/boostorg/charconv/issues/156. I guess I can knock them both out in a different PR.

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2024-04-11 06:20:59 UTC  
> Url: https://github.com/boostorg/charconv/pull/177#issuecomment-2048998169  

An automated preview of the documentation is available at [https://177.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://177.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---
