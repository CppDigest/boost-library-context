# #670 Fix #include inside boost namespace [Merged]

> Username: dscharrer  
> Created at: 2021-07-28 17:57:59 UTC  
> Updated at: 2021-08-02 17:34:17 UTC  
> Merged at: 2021-08-02 16:28:10 UTC  
> Closed at: 2021-08-02 16:28:11 UTC  
> Url: https://github.com/boostorg/math/pull/670  

The existing code fails to build if <utility> was not already included.

---

## Comment 1

> Username: jwakely  
> Created_at: 2021-08-02 10:47:41 UTC  
> Updated_at: 2021-08-02 11:09:05 UTC  
> Url: https://github.com/boostorg/math/pull/670#issuecomment-890925636  

This needs to be fixed in a 1.76.1 release because it's currently totally [broken](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=101722) with GCC trunk. I've refactored some libstdc++ headers so that the whole of `<utility>` is included less often by other headers in the library. Now it doesn't get implicitly included before reaching this bug, and so the first time it gets included is in the wrong namespace.

---

## Comment 2

> Username: jwakely  
> Created_at: 2021-08-02 10:53:52 UTC  
> Url: https://github.com/boostorg/math/pull/670#issuecomment-890928725  

The `#include` inside the boost::math namespace is obviously totally broken, but so is testing the `__cpp_lib_integer_sequence` feature test macro before including `<utility>`. Unless you include `<version>` (which isn't present on older implementations) then the only guaranteed way to get the macro is by including `<utility>`. So using it as the condition to decide whether or not to include `<utility>` is backwards.  
  
You *could* do:  
  
```c++  
#ifdef __has_include  
# if __has_include(<version>)  
#  include <version>  
#  define BOOST_MATH_HAVE_VERSION_HDR 1  
#endif   
#endif   
  
#ifdef BOOST_MATH_HAVE_VERSION_HDR  
# include <version>  
#else  
# include <utility>  
#endif  
  
....  
  
#ifdef __cpp_lib_integer_sequence  
```  
  
But frankly, you might as well just include `<utility>`.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-08-02 12:23:27 UTC  
> Url: https://github.com/boostorg/math/pull/670#issuecomment-890982765  

@mborland ?  
  
I'm wondering how this managed to slip through CI testing as well.

---

## Comment 4

> Username: jwakely  
> Created_at: 2021-08-02 12:44:14 UTC  
> Updated_at: 2021-08-02 12:48:25 UTC  
> Url: https://github.com/boostorg/math/pull/670#issuecomment-890994780  

> I'm wondering how this managed to slip through CI testing as well.  
  
Possible scenarios:  
  
- `<utility>` has already been included before that point, so the erroneous `#include <utility>` is a no-op thanks to its header guard. The bug doesn't show up.  
- `<utility>` has not been included yet, but that means the `__cpp_lib_integer_sequence` macro isn't defined, so you don't try to `#include <utility>` anyway. The bug doesn't show up.  
- `<utility>` has not been included, but some internal std::lib header which happens to define `__cpp_lib_integer_sequence` has been previously included. That means the macro is defined, so you try to `#include <utility>` inside the boost namespace. The bug shows up.  
  
Until last week, the third scenario wouldn't happen with libstdc++, because `std::integer_sequence` and its feature test macro were defined in `<utility>` itself. The way the macro got defined was by including that header, which means the erroneous `#include` is a no-op. Since last week, `std::integer_sequence` is in a new internal `<bits/xxx.h>` header that is used by `<utility>` but also some other headers such as `<tuple>`, so the third scenario is now possible.  
  
Presumably the third scenario also didn't happen with other implementations in your tests, although I suspect this would have triggered the bug using any impl:  
  
```c++  
#include <version>  
#include <boost/math/tools/mp.hpp>  
```  
  
It certainly fails with old versions of libstdc++ and with a recent libc++.

---

## Comment 5

> Username: jwakely  
> Created_at: 2021-08-02 12:53:04 UTC  
> Url: https://github.com/boostorg/math/pull/670#issuecomment-891000205  

A workaround for this bug is to `#include <utility>` before any use of boost 1.76.0 headers.

---

## Comment 6

> Username: mborland  
> Created_at: 2021-08-02 14:39:04 UTC  
> Url: https://github.com/boostorg/math/pull/670#issuecomment-891081625  

@jzmaddock Fortunately master for 1.77 is open until Wednesday so this fix is definitely worth cherry-picking.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2021-08-02 16:27:18 UTC  
> Url: https://github.com/boostorg/math/pull/670#issuecomment-891161877  

Test case is:  
  
```  
#include <version>  
#include <boost/math/tools/mp.hpp>  
```  
  
Note that if we replace `boost/math/tools/mp.hpp` with either of the public headers which include it: `boost/math/differentiation/autodiff.hpp` or `boost/math/policies/policy.hpp` then I don't see the issue (with gcc-10 at least), as these include <utility> albeit inadvertently via <algorithm>.  So it's not *quite* as grievous as it first appears, but does need fixing ASAP.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2021-08-02 16:27:51 UTC  
> Url: https://github.com/boostorg/math/pull/670#issuecomment-891162249  

Failing CI are server network issues, merging...

---

## Comment 9

> Username: pdimov  
> Created_at: 2021-08-02 16:47:29 UTC  
> Url: https://github.com/boostorg/math/pull/670#issuecomment-891174177  

`index_sequence` seems to be defined twice.  
https://github.com/boostorg/math/blob/3e46eba44a55350b7c2cb39901c0ff77c9b33a02/include/boost/math/tools/mp.hpp#L356-L357  
and  
https://github.com/boostorg/math/blob/3e46eba44a55350b7c2cb39901c0ff77c9b33a02/include/boost/math/tools/mp.hpp#L429-L430

---

## Comment 10

> Username: jwakely  
> Created_at: 2021-08-02 16:55:43 UTC  
> Updated_at: 2021-08-02 16:57:26 UTC  
> Url: https://github.com/boostorg/math/pull/670#issuecomment-891179206  

> then I don't see the issue (with gcc-10 at least), as these include albeit inadvertently via .  
  
(N.B. there's an HTML escaping issue here)  
  
Right, but with GCC's current trunk it fails much more readily, because `<algorithm>` no longer includes `<utility>` e.g. you get errors for realistic use cases like:  
  
```c++  
#include <boost/math/distributions/hypergeometric.hpp>  
boost::math::hypergeometric_distribution dist(50, 50, 500);  
```  
Admittedly GCC trunk isn't going to be released until Q2 2022, but it's likely to start appearing in distros sooner than that (maybe late 2021).

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2021-08-02 17:27:31 UTC  
> Url: https://github.com/boostorg/math/pull/670#issuecomment-891202671  

> index_sequence seems to be defined twice.  
  
You're correct - good catch, duplicate typedefs are allowed, but we shouldn't do that, will submit a fresh PR for that ASAP.  
  
>Right, but with GCC's current trunk it fails much more readily, because <algorithm> no longer includes <utility> e.g. you get errors for realistic use cases like:  
  
Nod.  It's asking for trouble.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2021-08-02 17:34:17 UTC  
> Url: https://github.com/boostorg/math/pull/670#issuecomment-891206636  

>index_sequence seems to be defined twice.  
  
See https://github.com/boostorg/math/pull/671

---
