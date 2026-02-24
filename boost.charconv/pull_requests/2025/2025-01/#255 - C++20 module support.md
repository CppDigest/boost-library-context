# #255 C++20 module support [Open]

> Username: anarthal  
> Created at: 2025-01-12 13:27:25 UTC  
> Updated at: 2025-04-08 10:51:59 UTC  
> Url: https://github.com/boostorg/charconv/pull/255  

Adds support for `import boost.charconv`.  
This pull request is intended to get feedback and support discussion in the ML. It depends on changes in Boost.CMake, Boost.Core, Boost.ThrowException and Boost.Assert that aren't in develop.  
Any feedback is welcome.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-01-12 14:04:04 UTC  
> Updated_at: 2025-01-12 14:16:31 UTC  
> Url: https://github.com/boostorg/charconv/pull/255#issuecomment-2585747759  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/255?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `94.11765%` with `1 line` in your changes missing coverage. Please review.  
> Project coverage is 94.85%. Comparing base [(`dacb62a`)](https://app.codecov.io/gh/boostorg/charconv/commit/dacb62a218c97918914f976800730e7534b56e8e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`65e5356`)](https://app.codecov.io/gh/boostorg/charconv/commit/65e535635e5bc14741101af39b2044151be6a0c0?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/255?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [...st/charconv/detail/fast\_float/digit\_comparison.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/255?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Ffast_float%2Fdigit_comparison.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZmFzdF9mbG9hdC9kaWdpdF9jb21wYXJpc29uLmhwcA==) | 50.00% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/charconv/pull/255?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/255/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/255?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #255   +/-   ##  
========================================  
  Coverage    94.85%   94.85%             
========================================  
  Files           69       69             
  Lines         9077     9077             
========================================  
  Hits          8610     8610             
  Misses         467      467             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/255?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/detail/apply\_sign.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/255?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fapply_sign.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvYXBwbHlfc2lnbi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/charconv/detail/buffer\_sizing.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/255?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fbuffer_sizing.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvYnVmZmVyX3NpemluZy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/charconv/detail/compute\_float32.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/255?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fcompute_float32.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvY29tcHV0ZV9mbG9hdDMyLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/charconv/detail/compute\_float64.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/255?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fcompute_float64.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvY29tcHV0ZV9mbG9hdDY0LmhwcA==) | `74.07% <ø> (ø)` | |  
| [include/boost/charconv/detail/compute\_float80.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/255?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fcompute_float80.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvY29tcHV0ZV9mbG9hdDgwLmhwcA==) | `91.30% <ø> (ø)` | |  
| [...lude/boost/charconv/detail/dragonbox/dragonbox.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/255?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fdragonbox%2Fdragonbox.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZHJhZ29uYm94L2RyYWdvbmJveC5ocHA=) | `97.68% <ø> (ø)` | |  
| [...ost/charconv/detail/dragonbox/dragonbox\_common.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/255?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fdragonbox%2Fdragonbox_common.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZHJhZ29uYm94L2RyYWdvbmJveF9jb21tb24uaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/charconv/detail/dragonbox/floff.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/255?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fdragonbox%2Ffloff.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZHJhZ29uYm94L2Zsb2ZmLmhwcA==) | `87.10% <ø> (ø)` | |  
| [include/boost/charconv/detail/emulated128.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/255?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Femulated128.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZW11bGF0ZWQxMjguaHBw) | `100.00% <100.00%> (ø)` | |  
| [...nclude/boost/charconv/detail/fallback\_routines.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/255?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Ffallback_routines.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZmFsbGJhY2tfcm91dGluZXMuaHBw) | `84.61% <ø> (ø)` | |  
| ... and [51 more](https://app.codecov.io/gh/boostorg/charconv/pull/255?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/255?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/255?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [dacb62a...65e5356](https://app.codecov.io/gh/boostorg/charconv/pull/255?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Commented]

> Username: mborland  
> Created_at: 2025-01-13 14:04:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/255#pullrequestreview-2546667982  

📁 CMakeLists.txt

```diff
  19 |+ 
  20 |+   # Enable and propagate C++23, import std, and the modules macro
  21 |+   target_compile_features(boost_charconv PUBLIC cxx_std_23)
```

> Username: mborland  
> Created_at: 2025-01-13 13:51:37 UTC  
> Updated_at: 2025-01-13 14:04:00 UTC  
> Url: https://github.com/boostorg/charconv/pull/255#discussion_r1913224131  

Have you tried this with C++20 instead of 23? I know officially `import std` and `import std.compat` are C++23, but all of the compiler implementors have said they'll allow it at 20.

> Username: anarthal  
> Created_at: 2025-01-13 15:00:02 UTC  
> Url: https://github.com/boostorg/charconv/pull/255#discussion_r1913331238  

Do you think there's a use to it? My understanding was that using C++20 over C++23 is about being able to use older compilers that might not support C++23. Such compilers won't have proper module implementations, likely, since modules are quite behind everything else.

> Username: mborland  
> Created_at: 2025-01-13 15:35:21 UTC  
> Updated_at: 2025-01-13 15:35:22 UTC  
> Url: https://github.com/boostorg/charconv/pull/255#discussion_r1913389635  

Standards like MISRA are pinned to language standards not toolchains. Theoretically someone could have a modern compiler but be stuck at C++20. Probably best to have someone in the real world complain before changing it.


📁 include/boost/charconv/detail/from_chars_integer_impl.hpp

```diff
  26 | 
  25 |- static constexpr unsigned char uchar_values[] =
  27 |+ BOOST_INLINE_CONSTEXPR unsigned char uchar_values[] =
```

> Username: mborland  
> Created_at: 2025-01-13 13:55:32 UTC  
> Updated_at: 2025-01-13 14:04:00 UTC  
> Url: https://github.com/boostorg/charconv/pull/255#discussion_r1913229768  

It is my understanding that the macros from other modules are not visible. Do we need to just copy and paste the logic we need from config into charconv/detail/config.hpp for this to work properly?

> Username: anarthal  
> Created_at: 2025-01-13 15:02:09 UTC  
> Url: https://github.com/boostorg/charconv/pull/255#discussion_r1913334497  

You can access macros via #include. This is the purpose of detail/global_module_fragment.hpp: all macros inside that file are made available to all headers in the library when building in module mode.  
  
I'll apply the suggestions that Peter made on the ML that might change how this works and will PR the changes if something better comes out.


📁 include/boost/charconv/detail/global_module_fragment.hpp

```diff
  16 |+ #include <boost/config.hpp>
  17 |+ #include <boost/assert.hpp>
  18 |+ 
```

> Username: mborland  
> Created_at: 2025-01-13 14:03:24 UTC  
> Updated_at: 2025-01-13 14:04:00 UTC  
> Url: https://github.com/boostorg/charconv/pull/255#discussion_r1913241651  

Would it be better instead of adding `#ifdef BOOST_USE_MODULES` in every file to move every include into this file and only include this file and other charconv files in the charconv files?  
  
eg  
  
```  
#include <boost/charconv/detail/global_module_fragement.hpp>  
#include <boost/charconv/detail/emulated128.hpp>  
ETC  
```  
  
In the top of a file? Then you could write some kind of code scanner that will error in your CI if a new contributor adds a STL header in a regular file which would break your module. Boost.Math has a CI run with Circle that does this to check for some of the boost guidelines (min/max, non-ascii characters, copyright, etc)


---

## Comment 3

> Username: mborland  
> Created_at: 2025-01-13 14:06:33 UTC  
> Url: https://github.com/boostorg/charconv/pull/255#issuecomment-2587198357  

This is cool progress. Thanks for continuing to plug away on it.

---

## Comment 4

> Username: Lastique  
> Created_at: 2025-04-07 20:58:29 UTC  
> Url: https://github.com/boostorg/charconv/pull/255#issuecomment-2784613210  

I think, instead of adding a bunch of headers in `include/boost/config/std`, a different approach would be better.  
  
Add a new macro in Boost.Config:  
  
```  
#if defined(BOOST_USE_MODULES)  
#define BOOST_STD_HEADER(x) <boost/config/detail/import_std.hpp>  
#else  
#define BOOST_STD_HEADER(x) <x>  
#endif  
```  
  
and a new `boost/config/detail/import_std.hpp` header:  
  
```  
#ifndef BOOST_CONFIG_DETAIL_IMPORT_STD_HPP_INCLUDED_  
#define BOOST_CONFIG_DETAIL_IMPORT_STD_HPP_INCLUDED_  
  
import std;  
  
#endif // BOOST_CONFIG_DETAIL_IMPORT_STD_HPP_INCLUDED_  
```  
  
Then in libraries standard headers can be included as such:  
  
```  
#include <boost/config.hpp> // for BOOST_STD_HEADER  
  
#include BOOST_STD_HEADER(utility)  
```  
  
This approach requires less maintenance and is forward compatible with std headers that might be added in the future.  
  
The above assumes that `BOOST_STD_HEADER` and `boost/config/detail/import_std.hpp` are added to Boost.Config, but this doesn't have to be the case. It can be a separate Boost library, if needed.

---

## Comment 5

> Username: pdimov  
> Created_at: 2025-04-07 23:24:52 UTC  
> Url: https://github.com/boostorg/charconv/pull/255#issuecomment-2784849989  

I think I prefer the normal `#include`, for aesthetic reasons if nothing else.

---

## Comment 6

> Username: anarthal  
> Created_at: 2025-04-08 10:51:58 UTC  
> Url: https://github.com/boostorg/charconv/pull/255#issuecomment-2786033624  

I'm good with any of the two approaches.

---
