# #217 rename collect to persist  [Merged]

> Username: alandefreitas  
> Created at: 2022-06-29 19:21:26 UTC  
> Updated at: 2022-08-03 21:07:08 UTC  
> Merged at: 2022-07-16 01:49:18 UTC  
> Closed at: 2022-07-16 01:49:18 UTC  
> Url: https://github.com/boostorg/url/pull/217  

fix #208  
  
BREAKING CHANGE: The function url_view::collect is now called url_view::persist

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2022-06-29 19:23:52 UTC  
> Url: https://github.com/boostorg/url/pull/217#issuecomment-1170398227  

@pdimov, @akrzemi1

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-06-29 19:29:51 UTC  
> Url: https://github.com/boostorg/url/pull/217#issuecomment-1170403481  

An automated preview of the documentation is available at [https://217.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://217.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-06-29 19:44:54 UTC  
> Url: https://github.com/boostorg/url/pull/217#issuecomment-1170416440  

An automated preview of the documentation is available at [https://217.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://217.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-06-29 22:37:40 UTC  
> Url: https://github.com/boostorg/url/pull/217#issuecomment-1170562769  

That commit message looks terrible

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2022-06-29 23:49:49 UTC  
> Updated_at: 2022-07-16 01:31:58 UTC  
> Url: https://github.com/boostorg/url/pull/217#issuecomment-1170601618  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/217?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#217](https://codecov.io/gh/CPPAlliance/url/pull/217?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (55ad694) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/1d1a09f6c00832463e1497a2643a3853c66de8e2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (1d1a09f) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 55ad694 differs from pull request most recent head f5de23a. Consider uploading reports for the commit f5de23a to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/217/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/217?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop     #217    +/-   ##  
=========================================  
  Coverage    96.77%   96.78%              
=========================================  
  Files          123      120     -3       
  Lines         6211     6068   -143       
=========================================  
- Hits          6011     5873   -138       
+ Misses         200      195     -5       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/217?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/authority\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/217/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/authority\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/217/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9hdXRob3JpdHlfdmlldy5pcHA=) | `96.27% <ø> (ø)` | |  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/217/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/url\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/217/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlldy5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/pct\_encoded\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/217/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wY3RfZW5jb2RlZF92aWV3LmlwcA==) | | |  
| [include/boost/url/impl/pct\_encoded\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/217/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wY3RfZW5jb2RlZF92aWV3LmhwcA==) | | |  
| [include/boost/url/pct\_encoded\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/217/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGN0X2VuY29kZWRfdmlldy5ocHA=) | | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/217?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/217?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [1d1a09f...f5de23a](https://codecov.io/gh/CPPAlliance/url/pull/217?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-06-30 00:15:23 UTC  
> Url: https://github.com/boostorg/url/pull/217#issuecomment-1170613853  

How many times can you say `url_view` in one commit message?

---

## Comment 7

> Username: alandefreitas  
> Created_at: 2022-06-30 05:57:56 UTC  
> Url: https://github.com/boostorg/url/pull/217#issuecomment-1170798764  

> That commit message looks terrible  
  
Fixed

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-06-30 06:04:50 UTC  
> Url: https://github.com/boostorg/url/pull/217#issuecomment-1170803338  

An automated preview of the documentation is available at [https://217.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://217.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-07-16 01:43:18 UTC  
> Url: https://github.com/boostorg/url/pull/217#issuecomment-1186052492  

An automated preview of the documentation is available at [https://217.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://217.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---
