# #19 Add CI framework and fix undefined behavior [Merged]

> Username: jeking3  
> Created at: 2018-06-13 17:37:50 UTC  
> Updated at: 2018-06-18 01:26:52 UTC  
> Merged at: 2018-06-14 12:13:25 UTC  
> Closed at: 2018-06-14 12:13:26 UTC  
> Url: https://github.com/boostorg/rational/pull/19  

- travis with valgrind, cppcheck, ubsan, codecov, covscan (future)  
  - appveyor with MSVC 2010 through 2017, cygwin 32/64, mingw 32/64  
  - README, LICENSE, etc.  
  
Implementing the CI framework exposed some undefined behavior in release mode builds which was also resolved.  
  
This fixes #18

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-06-13 18:21:32 UTC  
> Updated_at: 2018-06-14 03:23:42 UTC  
> Url: https://github.com/boostorg/rational/pull/19#issuecomment-397037096  

# [Codecov](https://codecov.io/gh/boostorg/rational/pull/19?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@ee177e6`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `0%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/rational/pull/19/graphs/tree.svg?width=650&height=150&src=pr&token=GWRPyIttVH)](https://codecov.io/gh/boostorg/rational/pull/19?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #19   +/-   ##  
==========================================  
  Coverage           ?   67.52%             
==========================================  
  Files              ?        1             
  Lines              ?      271             
  Branches           ?       90             
==========================================  
  Hits               ?      183             
  Misses             ?        3             
  Partials           ?       85  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/rational/pull/19?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/rational.hpp](https://codecov.io/gh/boostorg/rational/pull/19/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9yYXRpb25hbC5ocHA=) | `67.52% <0%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/rational/pull/19?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/rational/pull/19?src=pr&el=footer). Last update [ee177e6...76989ff](https://codecov.io/gh/boostorg/rational/pull/19?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: mclow  
> Created_at: 2018-06-13 18:27:47 UTC  
> Url: https://github.com/boostorg/rational/pull/19#issuecomment-397039045  

I like the fix. LGTM.   
  
(Also feeling good, because I nailed the cause: "undefined behavior", and bad because I should have identified the failing bit from the description)

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-06-14 19:40:22 UTC  
> Updated_at: 2018-06-14 19:44:53 UTC  
> Url: https://github.com/boostorg/rational/pull/19#issuecomment-397414673  

It looks like the implementation of `operator /=` has the same undefined behavior, potentially, but I'm not sure if the code above that would ever allow it to get that value.

---

## Comment 4

> Username: swatanabe  
> Created_at: 2018-06-14 19:48:45 UTC  
> Url: https://github.com/boostorg/rational/pull/19#issuecomment-397416990  

AMDG  
  
On 06/14/2018 01:40 PM, James E. King III wrote:  
> It looks like the implementation of `operator /=` has the same undefined behavior.  
>   
  
None of +=, -=, *=, and /= check for overflow at all.  
  
In Christ,  
Steven Watanabe

---
