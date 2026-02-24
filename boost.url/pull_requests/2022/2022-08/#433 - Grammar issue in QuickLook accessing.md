# #433 Grammar issue in QuickLook accessing. [Closed]

> Username: JonKalb  
> Created at: 2022-08-20 20:27:16 UTC  
> Updated at: 2022-09-15 16:16:40 UTC  
> Closed at: 2022-09-15 16:16:40 UTC  
> Url: https://github.com/boostorg/url/pull/433  

I'm not certain if this should be "…for both empty and…" or "…both for empty and…" (I think either would be correct), but I'm pretty certain that "…for both for…" is not correct.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-20 20:27:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/433#pullrequestreview-1079577932  

📁 doc/qbk/2.0.quicklook.qbk

```diff
 157 | [snippet_accessing_2a]
 158 | 
 159 |- for both for empty and absent components
```

> Username: vinniefalco  
> Created_at: 2022-08-20 20:27:57 UTC  
> Updated_at: 2022-08-20 20:27:58 UTC  
> Url: https://github.com/boostorg/url/pull/433#discussion_r950737953  

definitely not correct.. lol


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-08-20 20:35:21 UTC  
> Url: https://github.com/boostorg/url/pull/433#issuecomment-1221404270  

An automated preview of the documentation is available at [https://433.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://433.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-08-20 20:41:49 UTC  
> Updated_at: 2022-08-24 22:29:46 UTC  
> Url: https://github.com/boostorg/url/pull/433#issuecomment-1221405254  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/433?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#433](https://codecov.io/gh/CPPAlliance/url/pull/433?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (e26329a) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/88537b7177f0548623fd55ac83fee774d97025b9?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (88537b7) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/433/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/433?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #433   +/-   ##  
========================================  
  Coverage    97.92%   97.92%             
========================================  
  Files          132      132             
  Lines         6349     6349             
========================================  
  Hits          6217     6217             
  Misses         132      132             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/433?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/pct\_encoded\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/433/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGN0X2VuY29kZWRfdmlldy5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/433?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/433?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [88537b7...e26329a](https://codecov.io/gh/CPPAlliance/url/pull/433?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-08-20 22:35:23 UTC  
> Url: https://github.com/boostorg/url/pull/433#issuecomment-1221419922  

An automated preview of the documentation is available at [https://433.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://433.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-08-21 01:30:23 UTC  
> Url: https://github.com/boostorg/url/pull/433#issuecomment-1221442828  

An automated preview of the documentation is available at [https://433.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://433.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-08-21 05:05:42 UTC  
> Url: https://github.com/boostorg/url/pull/433#issuecomment-1221469428  

An automated preview of the documentation is available at [https://433.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://433.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-08-24 22:20:30 UTC  
> Url: https://github.com/boostorg/url/pull/433#issuecomment-1226501992  

An automated preview of the documentation is available at [https://433.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://433.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-09-09 19:19:25 UTC  
> Url: https://github.com/boostorg/url/pull/433#issuecomment-1242372645  

An automated preview of the documentation is available at [https://433.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://433.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---
