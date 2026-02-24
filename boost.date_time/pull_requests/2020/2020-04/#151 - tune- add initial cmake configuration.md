# #151 tune: add initial cmake configuration [Merged]

> Username: tapika  
> Created at: 2020-04-25 08:18:58 UTC  
> Updated at: 2020-05-18 20:59:30 UTC  
> Merged at: 2020-05-18 20:59:30 UTC  
> Closed at: 2020-05-18 20:59:30 UTC  
> Url: https://github.com/boostorg/date_time/pull/151  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-04-25 11:03:43 UTC  
> Updated_at: 2020-05-17 00:38:02 UTC  
> Url: https://github.com/boostorg/date_time/pull/151#issuecomment-619361806  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/151?src=pr&el=h1) Report  
> Merging [#151](https://codecov.io/gh/boostorg/date_time/pull/151?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/5093074e0eb7cdce268ac2ab925490f2b05b8e5e&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/151/graphs/tree.svg?width=650&height=150&src=pr&token=nDoh7t8f6g)](https://codecov.io/gh/boostorg/date_time/pull/151?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #151   +/-   ##  
========================================  
  Coverage    52.72%   52.72%             
========================================  
  Files           76       76             
  Lines         4491     4491             
  Branches      2243     2243             
========================================  
  Hits          2368     2368             
  Misses         391      391             
  Partials      1732     1732             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/151?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/151?src=pr&el=footer). Last update [5093074...89a1519](https://codecov.io/gh/boostorg/date_time/pull/151?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: JeffGarland  
> Created_at: 2020-05-03 15:04:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/151#pullrequestreview-404627295  

📁 CMakeLists.txt

```diff
   6 |+ project( BoostDateTime LANGUAGES CXX)
   7 |+ 
   8 |+ add_library( boost_date_time
```

> Username: JeffGarland  
> Created_at: 2020-05-03 15:00:52 UTC  
> Updated_at: 2020-05-16 21:40:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/151#discussion_r419115602  

As of 1.73 the library is just a stub for backward compatibility -- I don't think we should propagate the idea that it is needed in the cmake?

---

📁 CMakeLists.txt

```diff
  15 |+ target_link_libraries( boost_date_time
  16 |+     PUBLIC
  17 |+         Boost::config
```

> Username: JeffGarland  
> Created_at: 2020-05-03 15:04:28 UTC  
> Updated_at: 2020-05-16 21:40:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/151#discussion_r419116058  

my 'cmake foo' is used to older versions.  Does the 'PUBLIC' mean header only bc there's no library to link with boost::config.  And what about lexical_cast, shared_ptr, and other libraries?  serialization *is* needed to actually link if and only if you use the serialization code.

> Username: tapika  
> Created_at: 2020-05-16 21:48:07 UTC  
> Url: https://github.com/boostorg/date_time/pull/151#discussion_r426194127  

At the moment I'm not so sure about my cmake commits in overall. My current target is just to make boost / stacktrace working, but I have also slightly bigger questions in overall concerning boost build / cmake build system - I'm thinking of driving my changes in two directions - boost stacktrace would have standalone cmake support (independent from boost cmake), also I'm thinking of completely replacing boost build with cmake, but thinking is because I did not manage to get in touch with either boost build or boost cmake mail lists, suspect they are dead / not maintained.  
  
Threat my commits to date_time to cmake just an early prototype of cmake for date_time.

> Username: JeffGarland  
> Created_at: 2020-05-18 20:59:22 UTC  
> Url: https://github.com/boostorg/date_time/pull/151#discussion_r426890039  

Ok -- could we get a comment to that effect in the file?  I don't want people to get the idea that this is 'official' or something.  And I thought @pdimov was probably leading the cmake charge?


---
