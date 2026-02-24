# #139 Remove constructor from parse_options and add docs [Closed]

> Username: sdkrystian  
> Created at: 2020-08-10 17:07:48 UTC  
> Updated at: 2020-08-12 00:25:39 UTC  
> Closed at: 2020-08-12 00:25:39 UTC  
> Url: https://github.com/boostorg/json/pull/139  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-08-10 17:21:29 UTC  
> Url: https://github.com/boostorg/json/pull/139#issuecomment-671483336  

An automated preview of the documentation is available at [http://139.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://139.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-08-10 18:36:24 UTC  
> Url: https://github.com/boostorg/json/pull/139#issuecomment-671519594  

An automated preview of the documentation is available at [http://139.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://139.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Review 3 [Commented]

> Username: glenfe  
> Created_at: 2020-08-10 18:54:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/139#pullrequestreview-464477537  

All the test code changes seem largely unnecessary to me.

📁 test/basic_parser.cpp

```diff
 128 |+     opt.allow_invalid_utf8 = utf8;
 129 |+     return opt;
 130 |+ }
```

> Username: glenfe  
> Created_at: 2020-08-10 18:35:52 UTC  
> Updated_at: 2020-08-10 18:54:38 UTC  
> Url: https://github.com/boostorg/json/pull/139#discussion_r468100668  

Why do you need this?  `parse_options{ false, true, false }` works with `parse_options` as you've currently defined it.

> Username: sdkrystian  
> Created_at: 2020-08-10 20:23:57 UTC  
> Updated_at: 2020-08-10 20:23:58 UTC  
> Url: https://github.com/boostorg/json/pull/139#discussion_r468164923  

@glenfe Not for C++11; `parse_options` has default member initializers and thus is not an aggregate. C++14 and above doesn't have this problem.


---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2020-08-11 03:11:49 UTC  
> Updated_at: 2020-08-11 04:33:21 UTC  
> Url: https://github.com/boostorg/json/pull/139#issuecomment-671698869  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/139?src=pr&el=h1) Report  
> Merging [#139](https://codecov.io/gh/CPPAlliance/json/pull/139?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/ec29dfa768a16672534f758005dfe4841a8ea93d&el=desc) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/139/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/139?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #139      +/-   ##  
===========================================  
- Coverage    98.34%   98.34%   -0.01%       
===========================================  
  Files           63       62       -1       
  Lines         5754     5751       -3       
===========================================  
- Hits          5659     5656       -3       
  Misses          95       95                
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/139?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/139?src=pr&el=footer). Last update [ec29dfa...a169dec](https://codecov.io/gh/CPPAlliance/json/pull/139?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2020-08-11 23:02:20 UTC  
> Url: https://github.com/boostorg/json/pull/139#issuecomment-672339873  

Approved

---
