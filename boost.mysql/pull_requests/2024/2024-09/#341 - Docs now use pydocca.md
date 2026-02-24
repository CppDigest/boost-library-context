# #341 Docs now use pydocca [Merged]

> Username: anarthal  
> Created at: 2024-09-05 15:10:51 UTC  
> Updated at: 2024-09-06 14:40:06 UTC  
> Merged at: 2024-09-06 14:38:38 UTC  
> Closed at: 2024-09-06 14:38:38 UTC  
> Url: https://github.com/boostorg/mysql/pull/341  

close #334

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-09-06 12:55:01 UTC  
> Updated_at: 2024-09-06 14:40:06 UTC  
> Url: https://github.com/boostorg/mysql/pull/341#issuecomment-2333990542  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/341?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.03%. Comparing base [(`20335ac`)](https://app.codecov.io/gh/boostorg/mysql/commit/20335ac99e5e6d8e727bdfda3dba10c61a5a3ca4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`fcbdcf4`)](https://app.codecov.io/gh/boostorg/mysql/commit/fcbdcf433f5f68908ca26757c1014615f3a2c1f9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/341/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/341?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #341   +/-   ##  
========================================  
  Coverage    99.03%   99.03%             
========================================  
  Files          144      144             
  Lines         7122     7122             
========================================  
  Hits          7053     7053             
  Misses          69       69             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/341?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/any\_address.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/341?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fany_address.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9hbnlfYWRkcmVzcy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/client\_errc.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/341?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fclient_errc.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jbGllbnRfZXJyYy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/common\_server\_errc.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/341?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fcommon_server_errc.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb21tb25fc2VydmVyX2VycmMuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/341?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uLmhwcA==) | `98.91% <ø> (ø)` | |  
| [include/boost/mysql/connection\_pool.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/341?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection_pool.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uX3Bvb2wuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/escape\_string.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/341?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fescape_string.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9lc2NhcGVfc3RyaW5nLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/format\_sql.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/341?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fformat_sql.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9mb3JtYXRfc3FsLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/341?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/341?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [20335ac...fcbdcf4](https://app.codecov.io/gh/boostorg/mysql/pull/341?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
