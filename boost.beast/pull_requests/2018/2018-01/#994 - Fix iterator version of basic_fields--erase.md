# #994 Fix iterator version of basic_fields::erase [Closed]

> Username: inetic  
> Created at: 2018-01-22 15:08:31 UTC  
> Updated at: 2018-01-25 22:07:34 UTC  
> Closed at: 2018-01-25 22:07:34 UTC  
> Url: https://github.com/boostorg/beast/pull/994  

When used, was causing compile errors.  
  
Not sure whether const_casting here is the best approach,  
please let me know if you can think of a better solution and I'll  
change it.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2018-01-22 19:43:03 UTC  
> Url: https://github.com/boostorg/beast/pull/994#issuecomment-359541548  

>Not sure whether const_casting here is the best approach  
  
@pdimov suggests it is correct:  
  
* a precondition of deallocate is that the pointer came from allocate  
* allocate gives non-const pointers  
* therefore, the element can't have been const to begin with, and the const_cast is safe

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-01-23 02:41:52 UTC  
> Url: https://github.com/boostorg/beast/pull/994#issuecomment-359655349  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/994?src=pr&el=h1) Report  
> Merging [#994](https://codecov.io/gh/boostorg/beast/pull/994?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/be147865502feeac8c3feac18be6c255f8f45a97?src=pr&el=desc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/994/graphs/tree.svg?width=650&height=150&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/boostorg/beast/pull/994?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #994      +/-   ##  
===========================================  
- Coverage    95.45%   95.43%   -0.02%       
===========================================  
  Files          106      106                
  Lines        10601    10614      +13       
===========================================  
+ Hits         10119    10130      +11       
- Misses         482      484       +2  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/994?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/fields.ipp](https://codecov.io/gh/boostorg/beast/pull/994/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `97.75% <100%> (+0.02%)` | :arrow_up: |  
| [include/boost/beast/http/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/994/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `92.3% <0%> (-7.7%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/994?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/994?src=pr&el=footer). Last update [be14786...bc90b07](https://codecov.io/gh/boostorg/beast/pull/994?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: inetic  
> Created_at: 2018-01-24 08:39:40 UTC  
> Url: https://github.com/boostorg/beast/pull/994#issuecomment-360059057  

Thanks for the info, and happy it's OK. Out of curiosity, would you know what's happening with Travis? The Linux builds took about a day to start and the Mac build has been hanging there for more than two days now.

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-01-24 13:13:04 UTC  
> Url: https://github.com/boostorg/beast/pull/994#issuecomment-360127961  

Travis has a ~3000 macOS job backlog, https://www.traviscistatus.com/  
  
And the queued macOS jobs reduce the Linux parallelism from 5 to 3, so even Linux slows down.

---
