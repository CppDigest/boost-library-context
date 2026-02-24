# #1016 Provide the type trait alias get_lowest_layer_t [Closed]

> Username: djarek  
> Created at: 2018-02-10 21:47:44 UTC  
> Updated at: 2019-09-10 21:02:26 UTC  
> Closed at: 2018-02-22 00:54:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1016  

Added a C++14-style type alias (get_lowest_layer_t<T>) for the type trait get_lowest_layer<T>.  
  
Resolves: #941  
  
Signed-off-by: Damian Jarek <damian.jarek93@gmail.com>

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-02-10 22:10:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1016#issuecomment-364699337  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1016?src=pr&el=h1) Report  
> Merging [#1016](https://codecov.io/gh/boostorg/beast/pull/1016?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/908f84cb3c023ed7c5bd19e2e5283d3218a6d820?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1016/graphs/tree.svg?height=150&width=650&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1016?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1016   +/-   ##  
========================================  
  Coverage    95.43%   95.43%             
========================================  
  Files          106      106             
  Lines        10614    10614             
========================================  
  Hits         10130    10130             
  Misses         484      484  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1016?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1016?src=pr&el=footer). Last update [908f84c...6f560af](https://codecov.io/gh/boostorg/beast/pull/1016?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-02-11 02:05:44 UTC  
> Url: https://github.com/boostorg/beast/pull/1016#issuecomment-364712835  

But this requires C++14 does it not?

---

## Comment 3

> Username: glenfe  
> Created_at: 2018-02-12 01:08:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1016#issuecomment-364808768  

No, it requires only C++11 template aliases. (i.e. BOOST_NO_CXX11_TEMPLATE_ALIASES is not defined).

---
