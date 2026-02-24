# #31 Add UBSan invoker test [Closed]

> Username: Kojoley  
> Created at: 2018-10-23 01:07:54 UTC  
> Updated at: 2018-11-14 00:17:59 UTC  
> Closed at: 2018-11-14 00:17:59 UTC  
> Url: https://github.com/boostorg/function/pull/31  

ref #29

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-10-23 14:01:40 UTC  
> Updated_at: 2018-10-25 12:42:01 UTC  
> Url: https://github.com/boostorg/function/pull/31#issuecomment-432259315  

# [Codecov](https://codecov.io/gh/boostorg/function/pull/31?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@755d3c2`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/function/pull/31/graphs/tree.svg?width=650&token=beMMliwLU7&height=150&src=pr)](https://codecov.io/gh/boostorg/function/pull/31?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #31   +/-   ##  
==========================================  
  Coverage           ?   54.24%             
==========================================  
  Files              ?        4             
  Lines              ?      389             
  Branches           ?       98             
==========================================  
  Hits               ?      211             
  Misses             ?       85             
  Partials           ?       93  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/function/pull/31?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/function/pull/31?src=pr&el=footer). Last update [755d3c2...efe8f4a](https://codecov.io/gh/boostorg/function/pull/31?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-10-23 15:29:01 UTC  
> Url: https://github.com/boostorg/function/pull/31#issuecomment-432295536  

Interesting that it doesn't fail with g++. Perhaps they've fixed their sanitizer. :-)

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-10-23 15:42:45 UTC  
> Url: https://github.com/boostorg/function/pull/31#issuecomment-432301285  

> Interesting that it doesn't fail with g++  
  
I have spotted this yesterday https://github.com/boostorg/function/pull/29#issuecomment-432048409  
  
> Perhaps they've fixed their sanitizer. :-)  
  
I did not try the latest GCC, maybe it is a regression, or new inspection.

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-10-25 12:34:59 UTC  
> Url: https://github.com/boostorg/function/pull/31#issuecomment-433033067  

A very minor nit, perhaps, but placing the two UBSAN jobs together in `.travis.yml` would be nicer.

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-10-26 11:45:54 UTC  
> Url: https://github.com/boostorg/function/pull/31#issuecomment-433381119  

Thanks.  Now what do folks want to do with this one? :)

---

## Comment 6

> Username: jeking3  
> Created_at: 2018-11-01 12:41:08 UTC  
> Url: https://github.com/boostorg/function/pull/31#issuecomment-435028537  

So does this expose a bug in gcc-8 ubsan, or is it catching something that it never caught before but should have?

---

## Comment 7

> Username: Kojoley  
> Created_at: 2018-11-01 12:51:36 UTC  
> Url: https://github.com/boostorg/function/pull/31#issuecomment-435031186  

It depends on if hidden visibility flag on classes contained in multiple LTO units is considered ODR violation.

---

## Comment 8

> Username: pdimov  
> Created_at: 2018-11-14 00:17:50 UTC  
> Url: https://github.com/boostorg/function/pull/31#issuecomment-438489037  

A similar test to the one in this PR is now part of the develop branch (https://github.com/boostorg/function/commit/17716b63f2a27d7b2bf0a170da31fa5079a495ec).

---
