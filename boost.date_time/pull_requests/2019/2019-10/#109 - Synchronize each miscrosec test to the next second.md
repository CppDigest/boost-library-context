# #109 Synchronize each miscrosec test to the next second [Merged]

> Username: cclienti  
> Created at: 2019-10-06 09:21:13 UTC  
> Updated at: 2019-10-31 10:05:41 UTC  
> Merged at: 2019-10-08 14:17:02 UTC  
> Closed at: 2019-10-08 14:17:02 UTC  
> Url: https://github.com/boostorg/date_time/pull/109  

The aim is to avoid false positives in test_microsec when the seconds,  
minutes or hours change during time read between the second_clock and  
the microsec_clock.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-10-06 12:16:25 UTC  
> Updated_at: 2019-10-07 20:08:06 UTC  
> Url: https://github.com/boostorg/date_time/pull/109#issuecomment-538741403  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/109?src=pr&el=h1) Report  
> Merging [#109](https://codecov.io/gh/boostorg/date_time/pull/109?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/29bb2094646d0ffac43db4fc61a907306b2a8e68?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/109/graphs/tree.svg?width=650&token=nDoh7t8f6g&height=150&src=pr)](https://codecov.io/gh/boostorg/date_time/pull/109?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #109   +/-   ##  
========================================  
  Coverage    54.17%   54.17%             
========================================  
  Files           83       83             
  Lines         4984     4984             
  Branches      2368     2368             
========================================  
  Hits          2700     2700             
  Misses         398      398             
  Partials      1886     1886  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/109?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/109?src=pr&el=footer). Last update [29bb209...a6fd944](https://codecov.io/gh/boostorg/date_time/pull/109?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: JeffGarland  
> Created_at: 2019-10-06 22:51:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/109#pullrequestreview-297865616  

📁 test/posix_time/testmicrosec_time_clock.cpp

```diff
  71 |+     check("seconds match",
  72 |+           t1.time_of_day().seconds() == t2.time_of_day().seconds());
  73 |     check("hours date", t1.date() == t2.date());
```

> Username: JeffGarland  
> Created_at: 2019-10-06 22:51:38 UTC  
> Updated_at: 2019-10-07 20:08:04 UTC  
> Url: https://github.com/boostorg/date_time/pull/109#discussion_r331814999  

Not your bug, but description is a big funky here -- really should be something like "check date is the same"

> Username: cclienti  
> Created_at: 2019-10-07 05:23:17 UTC  
> Updated_at: 2019-10-07 20:08:04 UTC  
> Url: https://github.com/boostorg/date_time/pull/109#discussion_r331848189  

I can add a commit to the pull request to change check strings if you want?

> Username: JeffGarland  
> Created_at: 2019-10-07 11:23:16 UTC  
> Updated_at: 2019-10-07 20:08:04 UTC  
> Url: https://github.com/boostorg/date_time/pull/109#discussion_r331965956  

sure might as well 👍


---

## Review 3 [Approved]

> Username: JeffGarland  
> Created_at: 2019-10-06 22:55:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/date_time/pull/109#pullrequestreview-297865767  

cant see anything obviously amiss here.

---

## Comment 4

> Username: cclienti  
> Created_at: 2019-10-08 07:30:03 UTC  
> Url: https://github.com/boostorg/date_time/pull/109#issuecomment-539383998  

A test still fails in AppVeyor but I think that it is not related with the commit.  
  
Environment: FLAVOR=Visual Studio 2010, 2012, 2013, APPVEYOR_BUILD_WORKER_IMAGE=Visual Studio 2013, TOOLSET=msvc-10.0,msvc-11.0,msvc-12.0  
  
```  
LOCALAPPDATA=C:\Users\appveyor\AppData\Local  
could not find "vswhere"  
###  
### "Unknown toolset: vc12"  
###  
### You can specify the toolset as the argument, i.e.:  
###     .\build.bat msvc  
###  
### Toolsets supported by this script are: borland, como, gcc,  
###     gcc-nocygwin, intel-win32, metrowerks, mingw,  
###     vc11, vc12, vc14, vc141, vc142  
###  
### If you have Visual Studio 2017 installed you will need to either update  
### the Visual Studio 2017 installer or run from VS 2017 Command Prompt  
### as we where unable to detect your toolset installation.  
###  
C:\projects\boost-root>set lastexitcode=1   
```

---

## Comment 5

> Username: JeffGarland  
> Created_at: 2019-10-08 14:16:49 UTC  
> Url: https://github.com/boostorg/date_time/pull/109#issuecomment-539535092  

I agree -- the failure seems like a platform issue.

---
