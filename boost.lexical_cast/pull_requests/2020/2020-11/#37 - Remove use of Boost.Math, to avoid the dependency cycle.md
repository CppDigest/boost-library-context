# #37 Remove use of Boost.Math, to avoid the dependency cycle [Merged]

> Username: pdimov  
> Created at: 2020-11-22 20:30:36 UTC  
> Updated at: 2020-12-15 14:42:23 UTC  
> Merged at: 2020-12-15 14:42:23 UTC  
> Closed at: 2020-12-15 14:42:23 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/37  

This patch replaces the use of `isnan`, `changesign`, `signbit`, `isinf` from Boost.Math with `<math.h>`. I've tested it locally on Windows under msvc-8.0, msvc-10.0, msvc-11.0, msvc-12.0, msvc-14.0, msvc-14.1, clang-win, gcc (cygwin). Of these, it breaks msvc-8.0, 10.0, 11.0, because they don't have the functions in `<math.h>`, and introduces no new failures on the rest.  
  
Experiments on Godbolt suggest that all Linux gcc versions starting from 4.1 (earliest on Godbolt) have these functions. E.g. https://godbolt.org/z/WTMGr9  
  
Linux Clang, starting from 3.0 (earliest on Godbolt), also seems to work: https://godbolt.org/z/nr1Tvo  
  
What hasn't been tested: Apple Clang, any version.

---

## Comment 1

> Username: pdimov  
> Created_at: 2020-11-22 23:06:38 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/37#issuecomment-731862643  

Added emulation math functions for msvc-11.0 and below in a separate commit. With this additional change, msvc-8.0/10.0/11.0 are now unbroken (have no more failures than before.)

---

## Comment 2

> Username: pdimov  
> Created_at: 2020-11-23 02:15:38 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/37#issuecomment-731894014  

It's not so easy, as it turns out.

---

## Comment 3

> Username: pdimov  
> Created_at: 2020-11-23 15:27:27 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/37#issuecomment-732232309  

OK, now tested on Travis. The results of the current develop branch, as a baseline, are here: https://travis-ci.com/github/pdimov/lexical_cast/builds/203465518  
  
And the results with this PR applied are here: https://travis-ci.com/github/pdimov/lexical_cast/builds/203465561  
  
As far as I can see, it doesn't introduce any new errors. The macOS and clang-ubsan failures are preexistent. They should probably be cleaned up, but that's a separate exercise.

---
