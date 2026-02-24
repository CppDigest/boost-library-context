# #90 fixed hidden local variable [Merged]

> Username: BikingGlobetrotter  
> Created at: 2019-06-21 11:48:34 UTC  
> Updated at: 2019-11-22 05:06:13 UTC  
> Merged at: 2019-11-22 05:06:13 UTC  
> Closed at: 2019-11-22 05:06:13 UTC  
> Url: https://github.com/boostorg/process/pull/90  

Hi Boost Process maintainers,  
while compiling under windows, I faced a C4456 warning in boost::process::detail::windows::search_path  
( https://docs.microsoft.com/en-us/cpp/error-messages/compiler-warnings/compiler-warning-level-4-c4456 ) as declaration of 'pp' hides previous local declaration.   
  
In order to fix this warning, I renamed the internal variable inside the for loop.  
As we are compiling with "treat warning as error", we require this fix to be available in future releases of boost::process.   
  
Thanks for considering and best regards  
Michael

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-06-21 12:09:06 UTC  
> Url: https://github.com/boostorg/process/pull/90#issuecomment-504402664  

# [Codecov](https://codecov.io/gh/boostorg/process/pull/90?src=pr&el=h1) Report  
> Merging [#90](https://codecov.io/gh/boostorg/process/pull/90?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/process/commit/cf7ad3643818c84b308cdedb533b1c4553dd246c?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/process/pull/90/graphs/tree.svg?width=650&token=AhunMqTSpA&height=150&src=pr)](https://codecov.io/gh/boostorg/process/pull/90?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #90   +/-   ##  
========================================  
  Coverage    80.73%   80.73%             
========================================  
  Files          110      110             
  Lines         2621     2621             
========================================  
  Hits          2116     2116             
  Misses         505      505  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/process/pull/90?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/process/pull/90?src=pr&el=footer). Last update [cf7ad36...43523fc](https://codecov.io/gh/boostorg/process/pull/90?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
