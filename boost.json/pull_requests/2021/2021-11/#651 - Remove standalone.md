# #651 Remove standalone [Merged]

> Username: grisumbras  
> Created at: 2021-11-14 08:35:37 UTC  
> Updated at: 2022-02-11 14:22:28 UTC  
> Merged at: 2022-02-11 14:22:16 UTC  
> Closed at: 2022-02-11 14:22:17 UTC  
> Url: https://github.com/boostorg/json/pull/651  

Fix #650

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-11-14 08:41:26 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-968248138  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Review 2 [Commented]

> Username: phprus  
> Created_at: 2021-11-14 08:55:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/651#pullrequestreview-805446475  

📁 include/boost/json/detail/align.hpp

```diff
  68 |- using std::align;
  69 |- using max_align_t = std::max_align_t;
  70 |- #endif
```

> Username: phprus  
> Created_at: 2021-11-14 08:55:39 UTC  
> Updated_at: 2021-11-14 08:55:40 UTC  
> Url: https://github.com/boostorg/json/pull/651#discussion_r748823127  

Boost.JSON requires C++11.  
Why do you prefer the Boost instead of the features of the C++11 standard library?


---

## Review 3 [Commented]

> Username: phprus  
> Created_at: 2021-11-14 08:59:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/651#pullrequestreview-805446811  

📁 include/boost/json/system_error.hpp

```diff
  51 |- using std::generic_category;
  52 |- 
  53 |- #endif
```

> Username: phprus  
> Created_at: 2021-11-14 08:59:34 UTC  
> Updated_at: 2021-11-14 08:59:35 UTC  
> Url: https://github.com/boostorg/json/pull/651#discussion_r748823560  

Boost.JSON requires C++11.  
Why do you prefer the Boost instead of the features of the C++11 standard library?

> Username: grisumbras  
> Created_at: 2021-11-14 09:23:57 UTC  
> Url: https://github.com/boostorg/json/pull/651#discussion_r748826265  

As you can see, I simply removed the preprocessor logic for standalone mode. I presume, Boost.Align was used because some of the supported platforms do not have implementation for std::align and std::max_align_t. That is, some of the supported platforms implement only a subset of C++11.


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2021-11-14 09:49:03 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-968258139  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest-condensed-cd56452.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2021-11-19 10:31:16 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-973948496  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2021-11-19 11:36:16 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-973996845  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2021-11-19 11:46:14 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-974002484  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2021-11-19 11:56:17 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-974008320  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2021-11-19 12:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-974011411  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2021-11-19 12:06:18 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-974014847  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2021-11-19 12:16:12 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-974021664  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 12

> Username: codecov[bot]  
> Created_at: 2021-11-19 12:26:59 UTC  
> Updated_at: 2022-02-07 07:02:08 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-974029311  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/651?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#651](https://codecov.io/gh/boostorg/json/pull/651?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a9d9781) into [develop](https://codecov.io/gh/boostorg/json/commit/51395ed4d3eb9d0a3bd4b1eeeceb512e3cb8a997?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (51395ed) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/651/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/651?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #651   +/-   ##  
========================================  
  Coverage    99.04%   99.05%             
========================================  
  Files           68       69    +1       
  Lines         6315     6375   +60       
========================================  
+ Hits          6255     6315   +60       
  Misses          60       60             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/651?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/boostorg/json/pull/651/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/config.hpp](https://codecov.io/gh/boostorg/json/pull/651/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9jb25maWcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/string.hpp](https://codecov.io/gh/boostorg/json/pull/651/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/basic\_parser\_impl.hpp](https://codecov.io/gh/boostorg/json/pull/651/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `99.20% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/json/detail/impl/except.ipp](https://codecov.io/gh/boostorg/json/pull/651/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL2V4Y2VwdC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/651/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/error.ipp](https://codecov.io/gh/boostorg/json/pull/651/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvZXJyb3IuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/parse.hpp](https://codecov.io/gh/boostorg/json/pull/651/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/parser.hpp](https://codecov.io/gh/boostorg/json/pull/651/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlci5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/stream\_parser.hpp](https://codecov.io/gh/boostorg/json/pull/651/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmVhbV9wYXJzZXIuaHBw) | `100.00% <100.00%> (ø)` | |  
| ... and [2 more](https://codecov.io/gh/boostorg/json/pull/651/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/651?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/651?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [51395ed...a9d9781](https://codecov.io/gh/boostorg/json/pull/651?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2021-11-19 12:31:12 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-974032357  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2021-11-19 13:34:02 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-974077437  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest-condensed-87519eb.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2022-01-16 18:07:26 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1013924226  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2022-01-16 19:39:57 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1013938794  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest-condensed-33a9bc6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2022-01-27 16:11:27 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1023385870  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2022-01-27 16:56:20 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1023438131  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2022-01-27 18:04:08 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1023500545  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest-condensed-b21f4b9.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2022-02-06 10:36:21 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030804631  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2022-02-06 11:11:14 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030810556  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2022-02-06 12:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030818362  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2022-02-06 12:31:15 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030823352  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2022-02-06 13:06:13 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030828887  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2022-02-06 13:11:16 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030829797  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2022-02-06 14:06:24 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030839521  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 27

> Username: cppalliance-bot  
> Created_at: 2022-02-06 14:21:16 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030842189  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 28

> Username: cppalliance-bot  
> Created_at: 2022-02-06 15:28:55 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030855337  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest-condensed-b99f85e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html)

---

## Comment 29

> Username: cppalliance-bot  
> Created_at: 2022-02-06 16:01:20 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030861600  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 30

> Username: cppalliance-bot  
> Created_at: 2022-02-06 17:08:56 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030873734  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest-condensed-2162be9.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html)

---

## Comment 31

> Username: cppalliance-bot  
> Created_at: 2022-02-06 17:41:22 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030879508  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 32

> Username: cppalliance-bot  
> Created_at: 2022-02-06 18:48:56 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030891126  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest-condensed-84b74a7.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html)

---

## Comment 33

> Username: cppalliance-bot  
> Created_at: 2022-02-06 19:06:25 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030893975  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 34

> Username: cppalliance-bot  
> Created_at: 2022-02-06 19:26:21 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030897597  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 35

> Username: cppalliance-bot  
> Created_at: 2022-02-06 19:41:20 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030899908  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 36

> Username: cppalliance-bot  
> Created_at: 2022-02-06 19:56:18 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030902506  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 37

> Username: cppalliance-bot  
> Created_at: 2022-02-06 20:11:20 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030905221  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 38

> Username: cppalliance-bot  
> Created_at: 2022-02-06 21:18:57 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1030916598  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest-condensed-5168360.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html)

---

## Comment 39

> Username: cppalliance-bot  
> Created_at: 2022-02-07 06:56:27 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1031132518  

An automated preview of the documentation is available at [https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://651.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 40

> Username: cppalliance-bot  
> Created_at: 2022-02-07 07:58:57 UTC  
> Url: https://github.com/boostorg/json/pull/651#issuecomment-1031170956  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest-condensed-bd09d2b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/651/pullrequest.html)

---
