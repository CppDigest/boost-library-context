# #20 Drop dependency on MPL [Merged]

> Username: glenfe  
> Created at: 2018-09-22 19:59:38 UTC  
> Updated at: 2018-09-23 11:51:31 UTC  
> Merged at: 2018-09-23 01:44:30 UTC  
> Closed at: 2018-09-23 01:44:30 UTC  
> Url: https://github.com/boostorg/function/pull/20  

* Drop dependency on MPL  
* Use enable_if from TypeTraits  
* Use LightweightTest and NoExceptionsSupport from Core  
* Include workaround.hpp from config instead of detail

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-09-23 01:34:26 UTC  
> Url: https://github.com/boostorg/function/pull/20#issuecomment-423785126  

# [Codecov](https://codecov.io/gh/boostorg/function/pull/20?src=pr&el=h1) Report  
> Merging [#20](https://codecov.io/gh/boostorg/function/pull/20?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/function/commit/feea19660bcac246fc054f0a890415d01e884c4b?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `83.33%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/function/pull/20/graphs/tree.svg?width=650&token=beMMliwLU7&height=150&src=pr)](https://codecov.io/gh/boostorg/function/pull/20?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #20   +/-   ##  
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
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/function/pull/20?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/function/function\_base.hpp](https://codecov.io/gh/boostorg/function/pull/20/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mdW5jdGlvbi9mdW5jdGlvbl9iYXNlLmhwcA==) | `44.71% <0%> (ø)` | :arrow_up: |  
| [include/boost/function/function\_template.hpp](https://codecov.io/gh/boostorg/function/pull/20/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mdW5jdGlvbi9mdW5jdGlvbl90ZW1wbGF0ZS5ocHA=) | `64% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/function/pull/20?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/function/pull/20?src=pr&el=footer). Last update [feea196...c0d41a8](https://codecov.io/gh/boostorg/function/pull/20?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: podusowski  
> Created_at: 2018-09-23 10:53:35 UTC  
> Url: https://github.com/boostorg/function/pull/20#issuecomment-423807876  

Doesn't it brake pre-c++11 compatibility?

---

## Comment 3

> Username: glenfe  
> Created_at: 2018-09-23 11:34:51 UTC  
> Url: https://github.com/boostorg/function/pull/20#issuecomment-423809918  

No. What it used to use Boost.MPL for it now uses Boost.TypeTraits for. e.g. boost::mpl::mp_if_c to boost::conditional;  boost::mpl::int_ to boost::integral_constant.

---

## Comment 4

> Username: podusowski  
> Created_at: 2018-09-23 11:51:31 UTC  
> Url: https://github.com/boostorg/function/pull/20#issuecomment-423810910  

Right, sorry, I thought it was std:: one.

---
