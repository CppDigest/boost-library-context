# #287 Character set clean-up [Merged]

> Username: anarthal  
> Created at: 2024-06-23 15:25:35 UTC  
> Updated at: 2024-06-24 15:10:13 UTC  
> Merged at: 2024-06-24 15:10:13 UTC  
> Closed at: 2024-06-24 15:10:13 UTC  
> Url: https://github.com/boostorg/mysql/pull/287  

character_set::name is now a const char*, since NULL characters are not allowed by MySQL  
character_set::next_char doesn't need to be noexcept  
Removed noexcepts from next_char functions  
Internal refactor

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-06-24 12:09:48 UTC  
> Updated_at: 2024-06-24 13:29:04 UTC  
> Url: https://github.com/boostorg/mysql/pull/287#issuecomment-2186429211  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/287?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.47%. Comparing base [(`7736135`)](https://app.codecov.io/gh/boostorg/mysql/commit/77361353a59e03b6964e45e456cc0e0c783c98ee?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`065b583`)](https://app.codecov.io/gh/boostorg/mysql/commit/065b583b4705970288724a3c69b6d745a708f629?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/287/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/287?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #287      +/-   ##  
===========================================  
- Coverage    98.47%   98.47%   -0.01%       
===========================================  
  Files          136      136                
  Lines         6551     6547       -4       
===========================================  
- Hits          6451     6447       -4       
  Misses         100      100                
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/287?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/impl/character\_set.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/287?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fcharacter_set.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2NoYXJhY3Rlcl9zZXQuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/connection\_impl.ipp](https://app.codecov.io/gh/boostorg/mysql/pull/287?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fconnection_impl.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2Nvbm5lY3Rpb25faW1wbC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [...sql/impl/internal/sansio/connection\_state\_data.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/287?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fconnection_state_data.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9jb25uZWN0aW9uX3N0YXRlX2RhdGEuaHBw) | `100.00% <ø> (ø)` | |  
| [...t/mysql/impl/internal/sansio/set\_character\_set.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/287?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Finternal%2Fsansio%2Fset_character_set.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL2ludGVybmFsL3NhbnNpby9zZXRfY2hhcmFjdGVyX3NldC5ocHA=) | `97.36% <ø> (-0.14%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/287?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/287?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7736135...065b583](https://app.codecov.io/gh/boostorg/mysql/pull/287?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
