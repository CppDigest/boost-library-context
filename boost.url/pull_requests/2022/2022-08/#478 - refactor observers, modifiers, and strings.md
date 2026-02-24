# #478 refactor observers, modifiers, and strings [Merged]

> Username: vinniefalco  
> Created at: 2022-08-30 18:32:40 UTC  
> Updated at: 2022-09-01 18:03:30 UTC  
> Merged at: 2022-09-01 18:03:29 UTC  
> Closed at: 2022-09-01 18:03:29 UTC  
> Url: https://github.com/boostorg/url/pull/478  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-08-30 21:03:00 UTC  
> Updated_at: 2022-09-01 17:34:20 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1232165107  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/478?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#478](https://codecov.io/gh/CPPAlliance/url/pull/478?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (4f88068) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/18033bed8faafcf659502393b02b2d1801e91813?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (18033be) will **increase** coverage by `19.05%`.  
> The diff coverage is `86.54%`.  
  
> :exclamation: Current head 4f88068 differs from pull request most recent head 8478ace. Consider uploading reports for the commit 8478ace to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/478/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/478?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff              @@  
##           develop     #478       +/-   ##  
============================================  
+ Coverage    75.31%   94.37%   +19.05%       
============================================  
  Files          133      137        +4       
  Lines         6296     6577      +281       
============================================  
+ Hits          4742     6207     +1465       
+ Misses        1554      370     -1184       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/478?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/impl/except.ipp](https://codecov.io/gh/CPPAlliance/url/pull/478/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvZXhjZXB0LmlwcA==) | `50.00% <0.00%> (-10.00%)` | :arrow_down: |  
| [include/boost/url/impl/static\_url.ipp](https://codecov.io/gh/CPPAlliance/url/pull/478/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zdGF0aWNfdXJsLmlwcA==) | `91.30% <0.00%> (-8.70%)` | :arrow_down: |  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/CPPAlliance/url/pull/478/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `98.60% <ø> (+58.74%)` | :arrow_up: |  
| [include/boost/url/static\_url.hpp](https://codecov.io/gh/CPPAlliance/url/pull/478/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvc3RhdGljX3VybC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url.hpp](https://codecov.io/gh/CPPAlliance/url/pull/478/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/478/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view\_base.hpp](https://codecov.io/gh/CPPAlliance/url/pull/478/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <ø> (+2.98%)` | :arrow_up: |  
| [include/boost/url/impl/authority\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/478/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9hdXRob3JpdHlfdmlldy5pcHA=) | `75.67% <56.75%> (-19.07%)` | :arrow_down: |  
| [include/boost/url/detail/move\_chars.hpp](https://codecov.io/gh/CPPAlliance/url/pull/478/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL21vdmVfY2hhcnMuaHBw) | `83.33% <83.33%> (ø)` | |  
| [include/boost/url/detail/encode.hpp](https://codecov.io/gh/CPPAlliance/url/pull/478/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2VuY29kZS5ocHA=) | `88.11% <88.11%> (ø)` | |  
| ... and [31 more](https://codecov.io/gh/CPPAlliance/url/pull/478/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/478?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/478?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [18033be...8478ace](https://codecov.io/gh/CPPAlliance/url/pull/478?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-08-30 22:50:36 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1232249792  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-08-30 23:30:37 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1232274192  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-08-30 23:50:36 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1232285944  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-08-31 00:50:34 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1232322382  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-08-31 04:00:32 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1232432726  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-08-31 04:25:32 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1232446235  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-08-31 16:55:36 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1233187743  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-08-31 17:15:38 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1233207700  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-09-01 02:50:32 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1233672189  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-09-01 02:55:57 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1233674798  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-09-01 03:10:37 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1233683379  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-09-01 03:25:39 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1233692014  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2022-09-01 04:38:38 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1233731242  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2022-09-01 05:48:45 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1233770508  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2022-09-01 06:15:39 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1233792850  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2022-09-01 14:40:42 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1234377537  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2022-09-01 15:00:42 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1234403229  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2022-09-01 16:40:39 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1234525731  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2022-09-01 17:06:07 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1234552005  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2022-09-01 17:20:40 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1234567687  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2022-09-01 17:40:41 UTC  
> Url: https://github.com/boostorg/url/pull/478#issuecomment-1234587125  

An automated preview of the documentation is available at [https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://478.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---
