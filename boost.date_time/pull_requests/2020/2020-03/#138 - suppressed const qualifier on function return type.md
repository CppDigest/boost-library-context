# #138 suppressed const qualifier on function return type [Merged]

> Username: joaquintides  
> Created at: 2020-03-19 10:53:08 UTC  
> Updated at: 2020-03-19 16:21:21 UTC  
> Merged at: 2020-03-19 15:16:48 UTC  
> Closed at: 2020-03-19 15:16:48 UTC  
> Url: https://github.com/boostorg/date_time/pull/138  

As warned in https://www.boost.org/development/tests/develop/output/teeks99-dkr-dg4-6-warn-date_time-gcc-4-6~c++0x~warn-warnings.html#hours_special_value :  
```  
../boost/date_time/gregorian/greg_weekday.hpp:52:41: warning: type qualifiers ignored on function return type [-Wignored-qualifiers]  
../boost/date_time/gregorian/greg_weekday.hpp:61:40: warning: type qualifiers ignored on function return type [-Wignored-qualifiers]  
```

---

## Review 1 [Commented]

> Username: JeffGarland  
> Created_at: 2020-03-19 11:09:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/138#pullrequestreview-377620625  

Looks good - thanks.

---

## Comment 2

> Username: JeffGarland  
> Created_at: 2020-03-19 11:12:49 UTC  
> Url: https://github.com/boostorg/date_time/pull/138#issuecomment-601122271  

unfortunately the travis CI looks like it aborted for other reasons -- is there a way to restart it or do we just have to force push from command line?

---

## Comment 3

> Username: joaquintides  
> Created_at: 2020-03-19 11:24:33 UTC  
> Url: https://github.com/boostorg/date_time/pull/138#issuecomment-601126930  

I've taken a look and doesn't seem like there's a retry button available to me in Travis. Can't you restart either?

---

## Comment 4

> Username: JeffGarland  
> Created_at: 2020-03-19 11:29:46 UTC  
> Url: https://github.com/boostorg/date_time/pull/138#issuecomment-601128963  

There's no obvious way in github that I see unfortunately - which doesn't mean it isn't there, just that I've not encountered this.  I suspect I could override it by pushing from the command line -- or if you push a tweak to whitespace or something on the branch that will trigger it to run for sure.

---

## Comment 5

> Username: joaquintides  
> Created_at: 2020-03-19 12:08:13 UTC  
> Url: https://github.com/boostorg/date_time/pull/138#issuecomment-601142919  

Done, let's hope this time CI will get through

---

## Comment 6

> Username: joaquintides  
> Created_at: 2020-03-19 12:12:42 UTC  
> Updated_at: 2020-03-19 12:23:11 UTC  
> Url: https://github.com/boostorg/date_time/pull/138#issuecomment-601144606  

It's a systematic failure:  
```  
$ git clone https://github.com/boostorg/boost-ci.git boost-ci  
$ cp -pr boost-ci/ci boost-ci/.codecov.yml .  
cp: cannot stat 'boost-ci/.codecov.yml': No such file or directory  
The command "cp -pr boost-ci/ci boost-ci/.codecov.yml ." failed and exited with 1 during .  
```  
  
The problem is related to [this commit](https://github.com/boostorg/boost-ci/commit/d6c96a3636623240b6126222b67225d4bc603a6d).

---

## Comment 7

> Username: pdimov  
> Created_at: 2020-03-19 13:50:08 UTC  
> Url: https://github.com/boostorg/date_time/pull/138#issuecomment-601189420  

The offending change has been reverted and I restarted Travis, seems to work so far.

---

## Comment 8

> Username: codecov[bot]  
> Created_at: 2020-03-19 14:32:42 UTC  
> Updated_at: 2020-03-19 14:33:18 UTC  
> Url: https://github.com/boostorg/date_time/pull/138#issuecomment-601212601  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/138?src=pr&el=h1) Report  
> Merging [#138](https://codecov.io/gh/boostorg/date_time/pull/138?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/bbac2178ed3764e4d6f554402ceb432cc0aa3008?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/138/graphs/tree.svg?width=650&token=nDoh7t8f6g&height=150&src=pr)](https://codecov.io/gh/boostorg/date_time/pull/138?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #138   +/-   ##  
========================================  
  Coverage    52.68%   52.68%             
========================================  
  Files           76       76             
  Lines         4491     4491             
  Branches      2243     2243             
========================================  
  Hits          2366     2366             
  Misses         393      393             
  Partials      1732     1732  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/138?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/date\_time/gregorian/greg\_weekday.hpp](https://codecov.io/gh/boostorg/date_time/pull/138/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuL2dyZWdfd2Vla2RheS5ocHA=) | `88.88% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/138?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/138?src=pr&el=footer). Last update [bbac217...ae1f998](https://codecov.io/gh/boostorg/date_time/pull/138?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 9

> Username: JeffGarland  
> Created_at: 2020-03-19 15:16:41 UTC  
> Updated_at: 2020-03-19 16:21:21 UTC  
> Url: https://github.com/boostorg/date_time/pull/138#issuecomment-601237093  

Thanks guys

---
