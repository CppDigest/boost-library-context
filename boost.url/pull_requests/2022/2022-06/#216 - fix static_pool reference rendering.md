# #216 fix static_pool reference rendering  [Merged]

> Username: alandefreitas  
> Created at: 2022-06-29 18:34:48 UTC  
> Updated at: 2022-08-03 21:07:06 UTC  
> Merged at: 2022-07-16 02:22:41 UTC  
> Closed at: 2022-07-16 02:22:42 UTC  
> Url: https://github.com/boostorg/url/pull/216  

fix #212, #213

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-06-29 19:06:44 UTC  
> Url: https://github.com/boostorg/url/pull/216#issuecomment-1170383457  

While you're at it how about improving those docs?

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2022-06-29 20:54:32 UTC  
> Url: https://github.com/boostorg/url/pull/216#issuecomment-1170483787  

> While you're at it how about improving those docs?  
  
Done.   
  
Good idea. The docs were very poor.

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-06-29 20:54:54 UTC  
> Url: https://github.com/boostorg/url/pull/216#issuecomment-1170484015  

An automated preview of the documentation is available at [https://216.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://216.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-06-30 05:53:56 UTC  
> Updated_at: 2022-07-16 01:32:51 UTC  
> Url: https://github.com/boostorg/url/pull/216#issuecomment-1170795122  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/216?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#216](https://codecov.io/gh/CPPAlliance/url/pull/216?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (8492c59) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/1d1a09f6c00832463e1497a2643a3853c66de8e2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (1d1a09f) will **increase** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 8492c59 differs from pull request most recent head e46d0c0. Consider uploading reports for the commit e46d0c0 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/216/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/216?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop     #216    +/-   ##  
=========================================  
  Coverage    96.77%   96.78%              
=========================================  
  Files          123      120     -3       
  Lines         6211     6068   -143       
=========================================  
- Hits          6011     5873   -138       
+ Misses         200      195     -5       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/216?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/static\_pool.hpp](https://codecov.io/gh/CPPAlliance/url/pull/216/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvc3RhdGljX3Bvb2wuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/pct\_encoded\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/216/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wY3RfZW5jb2RlZF92aWV3LmhwcA==) | | |  
| [include/boost/url/pct\_encoded\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/216/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGN0X2VuY29kZWRfdmlldy5ocHA=) | | |  
| [include/boost/url/impl/pct\_encoded\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/216/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wY3RfZW5jb2RlZF92aWV3LmlwcA==) | | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/216?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/216?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [1d1a09f...e46d0c0](https://codecov.io/gh/CPPAlliance/url/pull/216?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-06-30 06:09:30 UTC  
> Url: https://github.com/boostorg/url/pull/216#issuecomment-1170806489  

An automated preview of the documentation is available at [https://216.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://216.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-07-16 01:48:02 UTC  
> Url: https://github.com/boostorg/url/pull/216#issuecomment-1186053162  

An automated preview of the documentation is available at [https://216.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://216.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-07-16 02:00:41 UTC  
> Url: https://github.com/boostorg/url/pull/216#issuecomment-1186060478  

An automated preview of the documentation is available at [https://216.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://216.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---
