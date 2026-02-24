# #115 Add missing headers [Closed]

> Username: camolezi  
> Created at: 2020-07-24 18:27:01 UTC  
> Updated at: 2020-08-10 00:56:10 UTC  
> Closed at: 2020-08-10 00:56:10 UTC  
> Url: https://github.com/boostorg/uuid/pull/115  

This makes the header be able to build standalone, making possible C++ clang modules builds.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-07-24 19:28:01 UTC  
> Updated_at: 2020-07-24 19:32:38 UTC  
> Url: https://github.com/boostorg/uuid/pull/115#issuecomment-663696818  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/115?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@ca0185f`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/115/graphs/tree.svg?width=650&height=150&src=pr&token=6falIr5npV)](https://codecov.io/gh/boostorg/uuid/pull/115?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop     #115   +/-   ##  
==========================================  
  Coverage           ?   82.00%             
==========================================  
  Files              ?       15             
  Lines              ?      639             
  Branches           ?      160             
==========================================  
  Hits               ?      524             
  Misses             ?       18             
  Partials           ?       97             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/uuid/pull/115?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/uuid/detail/uuid\_x86.ipp](https://codecov.io/gh/boostorg/uuid/pull/115/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC91dWlkX3g4Ni5pcHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/115?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/115?src=pr&el=footer). Last update [ca0185f...e44d862](https://codecov.io/gh/boostorg/uuid/pull/115?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: mclow  
> Created_at: 2020-07-24 20:18:55 UTC  
> Url: https://github.com/boostorg/uuid/pull/115#issuecomment-663716977  

I don't think this is quite right. It adds includes (including boost/config.h) _after_ some boost-specific checks. `BOOST_MSVC `.  Seems to me that those includes should go before that.

---
