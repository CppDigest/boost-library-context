# #361 error codes, rules, and test tidy [Merged]

> Username: vinniefalco  
> Created at: 2022-08-07 01:19:56 UTC  
> Updated at: 2022-08-07 23:38:50 UTC  
> Merged at: 2022-08-07 13:23:03 UTC  
> Closed at: 2022-08-07 13:23:03 UTC  
> Url: https://github.com/boostorg/url/pull/361  

fix #358

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-08-07 01:41:32 UTC  
> Updated_at: 2022-08-07 05:36:57 UTC  
> Url: https://github.com/boostorg/url/pull/361#issuecomment-1207312473  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/361?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#361](https://codecov.io/gh/CPPAlliance/url/pull/361?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (eea8cdd) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/9c6674cd4ef80d0962e15eb9bda1306f6e91d281?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (9c6674c) will **increase** coverage by `0.01%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head eea8cdd differs from pull request most recent head a9ec87a. Consider uploading reports for the commit a9ec87a to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/361/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/361?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #361      +/-   ##  
===========================================  
+ Coverage    96.26%   96.28%   +0.01%       
===========================================  
  Files          126      127       +1       
  Lines         6184     6187       +3       
===========================================  
+ Hits          5953     5957       +4       
+ Misses         231      230       -1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/361?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/grammar/delim\_rule.hpp](https://codecov.io/gh/CPPAlliance/url/pull/361/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9kZWxpbV9ydWxlLmhwcA==) | `100.00% <100.00%> (+4.54%)` | :arrow_up: |  
| [include/boost/url/grammar/impl/dec\_octet\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/361/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL2RlY19vY3RldF9ydWxlLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/grammar/impl/delim\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/361/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL2RlbGltX3J1bGUuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/grammar/impl/literal\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/361/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL2xpdGVyYWxfcnVsZS5pcHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/361?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/361?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [9c6674c...a9ec87a](https://codecov.io/gh/CPPAlliance/url/pull/361?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-08-07 05:30:19 UTC  
> Url: https://github.com/boostorg/url/pull/361#issuecomment-1207334240  

An automated preview of the documentation is available at [https://361.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://361.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-08-07 05:45:20 UTC  
> Url: https://github.com/boostorg/url/pull/361#issuecomment-1207335700  

An automated preview of the documentation is available at [https://361.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://361.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-08-07 05:50:31 UTC  
> Url: https://github.com/boostorg/url/pull/361#issuecomment-1207336259  

An automated preview of the documentation is available at [https://361.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://361.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---
