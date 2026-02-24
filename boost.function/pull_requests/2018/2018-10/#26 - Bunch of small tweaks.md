# #26 Bunch of small tweaks [Merged]

> Username: Kojoley  
> Created at: 2018-10-18 23:10:18 UTC  
> Updated at: 2018-10-22 23:36:55 UTC  
> Merged at: 2018-10-22 22:34:42 UTC  
> Closed at: 2018-10-22 22:34:42 UTC  
> Url: https://github.com/boostorg/function/pull/26  



---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-10-18 23:23:23 UTC  
> Url: https://github.com/boostorg/function/pull/26#issuecomment-431196204  

AMDG  
  
On 10/18/2018 05:10 PM, Nikita Kniazev wrote:  
>   * Replace boost::forward with static_cast  
  
This one is correct, but could use more explanation.  
Boost.Move provides move emulation in C++03, so  
this substitution is not generally correct, but  
it is in this case, because it's already guarded  
by BOOST_NO_CXX11_RVALUE_REFERENCES.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-10-19 00:16:45 UTC  
> Url: https://github.com/boostorg/function/pull/26#issuecomment-431204999  

Interesting, your other PR is failing with an error somewhere inside Boost.Test (a reminder maybe that we need to switch to lightweight_test), while this one passes. I don't offhand see a reason for that.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-10-19 00:19:35 UTC  
> Url: https://github.com/boostorg/function/pull/26#issuecomment-431205422  

I just factored out something from it before pushing to GH. I found one problem, but now I get infinity recursion in macros :disappointed:

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2018-10-19 00:27:36 UTC  
> Updated_at: 2018-10-19 00:29:06 UTC  
> Url: https://github.com/boostorg/function/pull/26#issuecomment-431206557  

# [Codecov](https://codecov.io/gh/boostorg/function/pull/26?src=pr&el=h1) Report  
> Merging [#26](https://codecov.io/gh/boostorg/function/pull/26?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/function/commit/453860ff9cfac59c53bd3936e95324b2e6318b97?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/function/pull/26/graphs/tree.svg?width=650&token=beMMliwLU7&height=150&src=pr)](https://codecov.io/gh/boostorg/function/pull/26?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #26   +/-   ##  
========================================  
  Coverage    54.24%   54.24%             
========================================  
  Files            4        4             
  Lines          389      389             
  Branches        98       98             
========================================  
  Hits           211      211             
  Misses          85       85             
  Partials        93       93  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/function/pull/26?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/function/function\_template.hpp](https://codecov.io/gh/boostorg/function/pull/26/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mdW5jdGlvbi9mdW5jdGlvbl90ZW1wbGF0ZS5ocHA=) | `64% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/function/pull/26?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/function/pull/26?src=pr&el=footer). Last update [453860f...8ed8a3d](https://codecov.io/gh/boostorg/function/pull/26?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-10-22 22:35:06 UTC  
> Url: https://github.com/boostorg/function/pull/26#issuecomment-432015077  

I rebased these in instead of squashing them since they all target different things.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2018-10-22 23:36:52 UTC  
> Url: https://github.com/boostorg/function/pull/26#issuecomment-432028957  

It was not need rebase because it was already on-top of master HEAD so it could be simply fast-forward merged, but for some reason GitHub merge button is the way it is :-).

---
