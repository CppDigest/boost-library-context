# #143 Value documentation fixes [Closed]

> Username: sdkrystian  
> Created at: 2020-08-10 20:57:26 UTC  
> Updated at: 2020-08-22 17:59:39 UTC  
> Closed at: 2020-08-12 17:11:34 UTC  
> Url: https://github.com/boostorg/json/pull/143  

Fixes #110

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-08-10 21:06:24 UTC  
> Url: https://github.com/boostorg/json/pull/143#issuecomment-671589728  

An automated preview of the documentation is available at [http://143.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://143.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-08-10 22:01:26 UTC  
> Url: https://github.com/boostorg/json/pull/143#issuecomment-671611769  

An automated preview of the documentation is available at [http://143.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://143.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2020-08-11 21:13:28 UTC  
> Updated_at: 2020-08-12 15:56:11 UTC  
> Url: https://github.com/boostorg/json/pull/143#issuecomment-672282549  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/143?src=pr&el=h1) Report  
> Merging [#143](https://codecov.io/gh/CPPAlliance/json/pull/143?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/ec29dfa768a16672534f758005dfe4841a8ea93d&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/143/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/143?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #143   +/-   ##  
========================================  
  Coverage    98.34%   98.34%             
========================================  
  Files           63       63             
  Lines         5754     5754             
========================================  
  Hits          5659     5659             
  Misses          95       95             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/143?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/143?src=pr&el=footer). Last update [ec29dfa...580f49e](https://codecov.io/gh/CPPAlliance/json/pull/143?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-11 22:42:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/143#pullrequestreview-465477291  

📁 test/snippets.cpp

```diff
 198 |+ 
 199 |+         value jv( string_kind );
 200 |+         if( auto ptr = jv.if_string() )
```

> Username: vinniefalco  
> Created_at: 2020-08-11 22:42:30 UTC  
> Updated_at: 2020-08-12 15:56:02 UTC  
> Url: https://github.com/boostorg/json/pull/143#discussion_r468905106  

no `auto` in docs


---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2020-08-12 16:01:29 UTC  
> Url: https://github.com/boostorg/json/pull/143#issuecomment-672963653  

An automated preview of the documentation is available at [http://143.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://143.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---
