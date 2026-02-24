# #612 Using SSE4.2 intrinsics in basic_parser if available [Closed]

> Username: octopus-prime  
> Created at: 2017-07-06 20:19:13 UTC  
> Updated at: 2017-07-06 22:36:17 UTC  
> Closed at: 2017-07-06 20:55:57 UTC  
> Url: https://github.com/boostorg/beast/pull/612  

issue #585

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-06 20:38:33 UTC  
> Updated_at: 2017-07-06 20:46:25 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313512628  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/612?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`v74@a0de36f`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `70.87%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/612/graphs/tree.svg?token=Gq6MFA9JRF&width=650&height=150&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/612?src=pr&el=tree)  
  
```diff  
@@          Coverage Diff           @@  
##             v74     #612   +/-   ##  
======================================  
  Coverage       ?   93.27%             
======================================  
  Files          ?      101             
  Lines          ?     7705             
  Branches       ?        0             
======================================  
  Hits           ?     7187             
  Misses         ?      518             
  Partials       ?        0  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/612?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/file.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/612?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZpbGUuaHBw) | `100% <ø> (ø)` | |  
| [include/beast/core/file\_posix.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/612?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZpbGVfcG9zaXguaHBw) | `100% <100%> (ø)` | |  
| [include/beast/core/detail/cpu\_info.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/612?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9jcHVfaW5mby5ocHA=) | `100% <100%> (ø)` | |  
| [include/beast/core/impl/file\_posix.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/612?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvZmlsZV9wb3NpeC5pcHA=) | `68.42% <68.42%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/612?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/612?src=pr&el=footer). Last update [a0de36f...548515c](https://codecov.io/gh/vinniefalco/Beast/pull/612?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: glenfe  
> Created_at: 2017-07-06 20:41:18 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313513287  

Why is this assuming that __get_cpuid exists just as long as the compiler is not MSVC (or some compiler pretending to be MSVC)?

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-07-06 20:45:06 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313514179  

This has to be rebased against the **v74** which I am about to put up - GitHub is behaving weirdly because these two branches are similar.

---

## Comment 4

> Username: octopus-prime  
> Created_at: 2017-07-06 20:46:03 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313514390  

@glenfe Since currently supported compilers are msvc, gcc and clang. IMO gcc and clang are happy with `__get_cpuid`

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2017-07-06 20:46:49 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313514575  

@glenfe What's a better way?

---

## Comment 6

> Username: octopus-prime  
> Created_at: 2017-07-06 20:49:17 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313515169  

> This has to be rebased against the v74 which I am about to put up - GitHub is behaving weirdly because these two branches are similar.  
  
any action needed by me?

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2017-07-06 20:50:28 UTC  
> Updated_at: 2017-07-06 20:50:41 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313515486  

>any action needed by me?  
  
Yes, just rebase it interactively (`git fetch upstream` then `git rebase -i upstream/v74` where `upstream` is the remote `git@github.com:vinniefalco/Beast.git`), delete the commits that aren't yours, and accept. Then force-push (to your repo, not the upstream)

---

## Comment 8

> Username: glenfe  
> Created_at: 2017-07-06 20:54:11 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313516394  

Supporting only three compilers is perfectly fine, but let's not assume that if not compiler X detected, that it must be one of Y or Z. i.e. The way the original code was detecting C++ implementations (i.e. an error directive with a meaningful message if not one of the supported compilers) was better.  
  
For something like this, it isn't a big deal, because ultimately it will just result in a compile time error anyway, but in general as a good practice for Boost libraries: If you're targeting implementation specifics, do it only after you've explicitly detected the implementation.

---

## Comment 9

> Username: glenfe  
> Created_at: 2017-07-06 20:56:07 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313516902  

Also, prefer use BOOST_MSVC from Boost.Config to determine if the compiler is truly MSVC, because compilers that choose to pretend to be MSVC may define _MSC_VER, and may not have the same behavior (or the same intrinsics).

---

## Comment 10

> Username: octopus-prime  
> Created_at: 2017-07-06 21:04:12 UTC  
> Updated_at: 2017-07-06 21:04:29 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313518873  

@glenfe   
  
Can you give a working example for this pseudocode using the right BOOST_xxx macros?  
  
```  
if msvc  
...  
else if gcc or clang  
...  
else  
...  
end  
```

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2017-07-06 21:04:45 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313519004  

I am confused on how to do this as well.

---

## Comment 12

> Username: glenfe  
> Created_at: 2017-07-06 21:22:48 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313523222  

For just that pseudo code:  
```  
#if defined(BOOST_MSVC) && BOOST_MSVC >= x  
// ...  
#elif (defined(BOOST_GCC) && BOOST_GCC > y) || (defined(BOOST_CLANG) && BOOST_CLANG > z)  
// ...  
#else  
// ...  
#endif  
```  
Of course, within each implementation detection block, you will want to check additional implementation specific things.

---

## Comment 13

> Username: octopus-prime  
> Created_at: 2017-07-06 21:35:12 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313525975  

@glenfe Thanks!  
@vinniefalco If we want to use these `x, y and z` versions - what are the minimal requirements for the compiler versions? I only found `When using Microsoft Visual C++, Visual Studio 2015 Update 3 or later is required.` So what versions are required for gcc and clang?  
  
BTW:  
Does anybody know whether we can use the clang version on OSX too?  
The Apple-LLVM compiler says something about version 7.x and 8.x.  
And http://llvm.org/ says something about a LLVM 5.0 will come **in future**.  
So clang versions on OSX are useless??

---

## Comment 14

> Username: vinniefalco  
> Created_at: 2017-07-06 21:42:11 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313527483  

Beast needs good C++11 support, so Visual Studio 2015 Update 3 is a reasonable minimum.  
  
Right now the farthest back Beast goes on gcc is 4.8. That's probably a reasonable lower limit, any lower and you start running into compiler bugs that are very hard to fix in Beast.  
  
For clang, I don't know.

---

## Comment 15

> Username: octopus-prime  
> Created_at: 2017-07-06 21:49:07 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313528907  

Ignoring the compiler versions for the moment.  
When we change the definition of `BEAST_NO_INTRINSICS` to  
  
```  
#ifndef BEAST_NO_INTRINSICS  
# if defined(BOOST_MSVC) || ((defined(BOOST_GCC) || defined(BOOST_CLANG)) && defined(__SSE4_2__))  
#  define BEAST_NO_INTRINSICS 0  
# else  
#  define BEAST_NO_INTRINSICS 1  
# endif  
#endif  
```  
  
and all other cpuid and SSE4.2 stuff will guarded by `!BEAST_NO_INTRINSICS` so we are safe with  
  
```  
#if defined(BOOST_MSVC)  
#include <intrin.h> // __cpuid  
#else  
#include <cpuid.h>  // __get_cpuid  
#endif  
```  
  
?!  
  
@glenfe @vinniefalco What do you think?

---

## Comment 16

> Username: vinniefalco  
> Created_at: 2017-07-06 22:36:17 UTC  
> Url: https://github.com/boostorg/beast/pull/612#issuecomment-313537931  

I don't know how any of those BOOST macros work, and I am unfamiliar with the other toolchains' macros. So I cannot evaluate this change.

---
