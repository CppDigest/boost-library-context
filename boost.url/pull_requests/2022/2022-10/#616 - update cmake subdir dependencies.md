# #616 update cmake subdir dependencies [Merged]

> Username: alandefreitas  
> Created at: 2022-10-31 21:22:26 UTC  
> Updated at: 2022-12-22 16:13:06 UTC  
> Merged at: 2022-11-07 19:31:56 UTC  
> Closed at: 2022-11-07 19:31:56 UTC  
> Url: https://github.com/boostorg/url/pull/616  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-31 21:35:56 UTC  
> Url: https://github.com/boostorg/url/pull/616#issuecomment-1297715171  

GCOVR code coverage report [https://616.url.prtest.cppalliance.org/gcovr/index.html](https://616.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://616.url.prtest.cppalliance.org/genhtml/index.html](https://616.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-10-31 21:50:20 UTC  
> Updated_at: 2022-11-07 18:32:27 UTC  
> Url: https://github.com/boostorg/url/pull/616#issuecomment-1297728809  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/616?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#616](https://codecov.io/gh/boostorg/url/pull/616?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (80cb297) into [develop](https://codecov.io/gh/boostorg/url/commit/643d959e3db8b59cf3f065ec1738cd0197c70b02?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (643d959) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/616/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/616?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #616   +/-   ##  
========================================  
  Coverage    96.47%   96.47%             
========================================  
  Files          138      138             
  Lines         6727     6727             
========================================  
  Hits          6490     6490             
  Misses         237      237             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/616?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/over\_allocator.hpp](https://codecov.io/gh/boostorg/url/pull/616/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL292ZXJfYWxsb2NhdG9yLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/616?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/616?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [643d959...80cb297](https://codecov.io/gh/boostorg/url/pull/616?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-11-03 23:54:20 UTC  
> Url: https://github.com/boostorg/url/pull/616#issuecomment-1302802563  

Because JSON?

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-11-04 20:07:02 UTC  
> Updated_at: 2022-11-04 21:06:45 UTC  
> Url: https://github.com/boostorg/url/pull/616#issuecomment-1304130033  

I don't even know. This list changes all the time.  
  
This lists all potential dependencies so that's why the add_subdirectory directory is conditional. Because a transitive dependency sometimes exists in `boostorg/boost#master` but not in `boostorg/boost#develop` and vice-versa.  
  
I usually just find out about a new dependency when some other library changes and things break. But I don't know at what point in time, through what library, and in what branch each of these transitive dependencies existed.  
  
And the test before `add_subdirectory` is important before it would be unreasonable to have a different list of dependencies for `develop` and `master`.  
  
Some of these dependencies might not even exist anymore. What really counts is the current dependency report. I guess we could try to prune these dependencies every once in a while, but I'm not sure this is worth the effort since the inclusion is conditional anyway.

---

## Comment 5

> Username: alandefreitas  
> Created_at: 2022-11-04 20:08:16 UTC  
> Updated_at: 2022-11-04 20:08:41 UTC  
> Url: https://github.com/boostorg/url/pull/616#issuecomment-1304132852  

Actually, it's because of boost_container_hash:  
  
https://github.com/boostorg/url/actions/runs/3364254929/jobs/5578393129  
  
We should probably merge this before merging https://github.com/boostorg/url/pull/615  
  
This is the reason why https://github.com/boostorg/url/pull/615 fails in CI

---

## Comment 6

> Username: pdimov  
> Created_at: 2022-11-04 23:44:59 UTC  
> Url: https://github.com/boostorg/url/pull/616#issuecomment-1304342355  

You can see what depends on what and why using `boostdep`. For example, the dependency list you need for add_subdirectory use is `boostdep --brief url`. You can see why your library has the primary dependencies it has with `boostdep --primary url` and the indirect dependencies can be seen with `boostdep --secondary url`.  
  
The same information is available in an easier to browse way at https://pdimov.github.io/boostdep-report/ (e.g. https://pdimov.github.io/boostdep-report/develop/url.html), but I only regenerate that occasionally, so it might not be entirely up to date.

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-11-05 00:19:08 UTC  
> Url: https://github.com/boostorg/url/pull/616#issuecomment-1304353216  

GCOVR code coverage report [https://616.url.prtest.cppalliance.org/gcovr/index.html](https://616.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://616.url.prtest.cppalliance.org/genhtml/index.html](https://616.url.prtest.cppalliance.org/genhtml/index.html)

---
