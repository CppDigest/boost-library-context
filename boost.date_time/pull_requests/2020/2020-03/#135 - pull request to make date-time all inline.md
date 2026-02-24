# #135 pull request to make date-time all inline [Merged]

> Username: JeffGarland  
> Created at: 2020-03-15 00:20:47 UTC  
> Updated at: 2020-03-16 02:06:33 UTC  
> Merged at: 2020-03-15 11:27:40 UTC  
> Closed at: 2020-03-15 11:27:40 UTC  
> Url: https://github.com/boostorg/date_time/pull/135  

See issue #134.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-03-15 02:33:31 UTC  
> Updated_at: 2020-03-15 02:48:31 UTC  
> Url: https://github.com/boostorg/date_time/pull/135#issuecomment-599159621  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/135?src=pr&el=h1) Report  
> Merging [#135](https://codecov.io/gh/boostorg/date_time/pull/135?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/139f44776d8713a8b2967feaf6f0deb41ccd2371&el=desc) will **decrease** coverage by `0.76%`.  
> The diff coverage is `46.42%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/135/graphs/tree.svg?width=650&height=150&src=pr&token=nDoh7t8f6g)](https://codecov.io/gh/boostorg/date_time/pull/135?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #135      +/-   ##  
===========================================  
- Coverage    53.44%   52.68%   -0.77%       
===========================================  
  Files           83       76       -7       
  Lines         4949     4491     -458       
  Branches      2410     2243     -167       
===========================================  
- Hits          2645     2366     -279       
+ Misses         420      393      -27       
+ Partials      1884     1732     -152       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/135?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/date\_time/date\_generators.hpp](https://codecov.io/gh/boostorg/date_time/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZGF0ZV9nZW5lcmF0b3JzLmhwcA==) | `49.31% <ø> (-0.69%)` | :arrow_down: |  
| [include/boost/date\_time/date\_parsing.hpp](https://codecov.io/gh/boostorg/date_time/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZGF0ZV9wYXJzaW5nLmhwcA==) | `33.01% <0.00%> (-9.41%)` | :arrow_down: |  
| [include/boost/date\_time/find\_match.hpp](https://codecov.io/gh/boostorg/date_time/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZmluZF9tYXRjaC5ocHA=) | `80.00% <80.00%> (ø)` | |  
| [include/boost/date\_time/gregorian/greg\_month.hpp](https://codecov.io/gh/boostorg/date_time/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuL2dyZWdfbW9udGguaHBw) | `93.33% <100.00%> (+26.66%)` | :arrow_up: |  
| [include/boost/date\_time/gregorian/greg\_weekday.hpp](https://codecov.io/gh/boostorg/date_time/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuL2dyZWdfd2Vla2RheS5ocHA=) | `88.88% <100.00%> (+22.22%)` | :arrow_up: |  
| [include/boost/date\_time/time\_resolution\_traits.hpp](https://codecov.io/gh/boostorg/date_time/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdGltZV9yZXNvbHV0aW9uX3RyYWl0cy5ocHA=) | `96.15% <100.00%> (ø)` | |  
| [...clude/boost/date\_time/local\_time/local\_time\_io.hpp](https://codecov.io/gh/boostorg/date_time/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvbG9jYWxfdGltZS9sb2NhbF90aW1lX2lvLmhwcA==) | `45.00% <0.00%> (-31.09%)` | :arrow_down: |  
| [include/boost/date\_time/time\_system\_split.hpp](https://codecov.io/gh/boostorg/date_time/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdGltZV9zeXN0ZW1fc3BsaXQuaHBw) | `25.80% <0.00%> (-13.44%)` | :arrow_down: |  
| [include/boost/date\_time/gregorian/formatters.hpp](https://codecov.io/gh/boostorg/date_time/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuL2Zvcm1hdHRlcnMuaHBw) | `63.33% <0.00%> (-8.41%)` | :arrow_down: |  
| [...ude/boost/date\_time/posix\_time/time\_formatters.hpp](https://codecov.io/gh/boostorg/date_time/pull/135/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvcG9zaXhfdGltZS90aW1lX2Zvcm1hdHRlcnMuaHBw) | `29.47% <0.00%> (-2.31%)` | :arrow_down: |  
| ... and [30 more](https://codecov.io/gh/boostorg/date_time/pull/135/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/135?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/135?src=pr&el=footer). Last update [139f447...7cc1ac2](https://codecov.io/gh/boostorg/date_time/pull/135?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: pdimov  
> Created_at: 2020-03-15 22:30:53 UTC  
> Url: https://github.com/boostorg/date_time/pull/135#issuecomment-599273701  

This change breaks all Jamfiles that refer to date_time, and will break all Linux packages that link to it. (And all CMake projects that use the `date_time` component.)  
  
When I made System header-only, I left a stub library there to avoid this.

---

## Comment 3

> Username: JeffGarland  
> Created_at: 2020-03-16 00:58:15 UTC  
> Updated_at: 2020-03-16 00:58:53 UTC  
> Url: https://github.com/boostorg/date_time/pull/135#issuecomment-599292673  

Ok I'll do that - I'm sure users will have the same issue.  But also isn't it better to submit pull requests for libs to end the dependence?  Note that was my intent, but my regex (obviously bad) didn't turn up the dependencies...

---

## Comment 4

> Username: pdimov  
> Created_at: 2020-03-16 01:17:04 UTC  
> Url: https://github.com/boostorg/date_time/pull/135#issuecomment-599295936  

There are hundreds if not thousands of packages outside Boost that either do the equivalent of `-lboost_date_time` in their Makefile or `find_package(Boost COMPONENTS date_time)` in their CMakeLists.txt - we can't find and fix them all; this will create problems for downstream packagers such as Debian etc.  
  
Inside of Boost, yes, we can do that. I think that most places that linked to `/boost//system` are gone by now.  
  
There are three ways to refer to DateTime from a Jamfile - `/boost//date_time`, `/boost/date_time//boost_date_time`, and `...path-to-libs.../date_time/build//boost_date_time`. (Maybe more, I'm not sure.)

---

## Comment 5

> Username: JeffGarland  
> Created_at: 2020-03-16 01:32:11 UTC  
> Url: https://github.com/boostorg/date_time/pull/135#issuecomment-599298850  

Stub coming soon.  So does the stub live forever then?  I was wondering what the distros like redhat, etc would do with this...

---

## Comment 6

> Username: pdimov  
> Created_at: 2020-03-16 01:39:48 UTC  
> Url: https://github.com/boostorg/date_time/pull/135#issuecomment-599300208  

I don't think I can remove the System stub anytime soon. There's no mechanism to inform people that they need to fix their Makefiles. Distros just ship libboost_system.a/.so as they've always done.

---

## Comment 7

> Username: JeffGarland  
> Created_at: 2020-03-16 02:06:33 UTC  
> Url: https://github.com/boostorg/date_time/pull/135#issuecomment-599305296  

ok, thanks for the assist.  pr #137 will go to develop as soon as the CI finishes.

---
