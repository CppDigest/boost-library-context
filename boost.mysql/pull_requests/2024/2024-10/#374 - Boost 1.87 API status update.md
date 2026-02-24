# #374 Boost 1.87 API status update [Merged]

> Username: anarthal  
> Created at: 2024-10-28 16:09:24 UTC  
> Updated at: 2024-10-29 17:03:17 UTC  
> Merged at: 2024-10-29 17:02:55 UTC  
> Closed at: 2024-10-29 17:02:55 UTC  
> Url: https://github.com/boostorg/mysql/pull/374  

any_connection, client-side SQL formatting and connection_pool are now stable  
connection is now marked as legacy (not recommended for new code)  
Added legacy/experimental tags in quickref file  
  
close #364

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-10-28 21:59:48 UTC  
> Updated_at: 2024-10-29 17:03:17 UTC  
> Url: https://github.com/boostorg/mysql/pull/374#issuecomment-2442734010  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/374?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.04%. Comparing base [(`dafe04a`)](https://app.codecov.io/gh/boostorg/mysql/commit/dafe04a38d5937f3b0ab32541ba8a3267e08e8e9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`1e241b3`)](https://app.codecov.io/gh/boostorg/mysql/commit/1e241b3335b397b95572e8ef3831dde6fd2b7d66?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/374/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/374?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #374   +/-   ##  
========================================  
  Coverage    99.04%   99.04%             
========================================  
  Files          143      143             
  Lines         7157     7157             
========================================  
  Hits          7089     7089             
  Misses          68       68             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/mysql/pull/374?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/any\_address.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/374?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fany_address.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9hbnlfYWRkcmVzcy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/any\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/374?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fany_connection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9hbnlfY29ubmVjdGlvbi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/buffer\_params.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/374?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fbuffer_params.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9idWZmZXJfcGFyYW1zLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/client\_errc.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/374?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fclient_errc.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jbGllbnRfZXJyYy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/374?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uLmhwcA==) | `98.91% <ø> (ø)` | |  
| [include/boost/mysql/connection\_pool.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/374?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection_pool.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uX3Bvb2wuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/constant\_string\_view.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/374?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconstant_string_view.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25zdGFudF9zdHJpbmdfdmlldy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/escape\_string.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/374?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fescape_string.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9lc2NhcGVfc3RyaW5nLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/format\_sql.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/374?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fformat_sql.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9mb3JtYXRfc3FsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/handshake\_params.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/374?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fhandshake_params.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9oYW5kc2hha2VfcGFyYW1zLmhwcA==) | `100.00% <ø> (ø)` | |  
| ... and [3 more](https://app.codecov.io/gh/boostorg/mysql/pull/374?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/374?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/374?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [dafe04a...1e241b3](https://app.codecov.io/gh/boostorg/mysql/pull/374?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
