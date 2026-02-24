# #328 Added with_diagnostics completion token [Merged]

> Username: anarthal  
> Created at: 2024-08-07 19:32:10 UTC  
> Updated at: 2024-08-13 08:48:51 UTC  
> Merged at: 2024-08-13 08:48:49 UTC  
> Closed at: 2024-08-13 08:48:49 UTC  
> Url: https://github.com/boostorg/mysql/pull/328  

Made with_diagnostics(deferred) the default token for any_connection and connection_pool.  
throw_on_error is now marked as legacy.  
  
close #329   
close #296

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2024-08-07 23:31:55 UTC  
> Url: https://github.com/boostorg/mysql/pull/328#issuecomment-2274516510  

That would look good to me if Chris didn't [add partial completion tokens](https://github.com/chriskohlhoff/asio/commit/1a0008e994ef233cbc581f0b7c2c813133a4e25e) :). This should be legal:  
  
```cpp  
co_await conn.async_prepare_statement(..., with_diagnostics);  
```  
  
In order for that to work, you'll need to   
  
1. make `with_diagnostic` a token itself, e.g.   
  
```cpp  
constexpr static partial_with_diagnostic with_diagnostic;  
```  
  
And then do this   
  
```cpp  
template <typename... Signatures>  
struct async_result<boost::mysql::partial_with_diagnostic, Signatures...>  
{  
  template <typename Initiation, typename RawCompletionToken, typename... Args>  
  static auto initiate(Initiation&& initiation,  
      RawCompletionToken&&, Args&&... args)  
    -> decltype(  
      async_initiate<  
        const as_tuple_t<  
          default_completion_token_t<associated_executor_t<Initiation>>>&,  
        Signatures...>(  
          static_cast<Initiation&&>(initiation),  
          boost::mysql::with_diagnostics_t<  
            default_completion_token_t<associated_executor_t<Initiation>>>{},  
          static_cast<Args&&>(args)...))  
  {  
    return async_initiate<  
      const as_tuple_t<  
        default_completion_token_t<associated_executor_t<Initiation>>>&,  
      Signatures...>(  
        static_cast<Initiation&&>(initiation),  
        boost::mysql::with_diagnostics_t<  
          default_completion_token_t<associated_executor_t<Initiation>>>{},  
        static_cast<Args&&>(args)...);  
  }  
};  
```  
  
For that to work your initiations need an associated_executor (executor_type typedef & get_executor function). They should have that anyhow so all of asio's `cancel_after` and `cancel_at` adaptors/partial tokens work. Some of them grab the executor the same way so this is valid:  
  
```cpp  
co_await conn.async_prepare_statement(... , asio::cancel_after(std::chrono::seconds(5));  
```  
  
Sorry for giving you more work.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-08-08 16:19:20 UTC  
> Updated_at: 2024-08-12 10:11:48 UTC  
> Url: https://github.com/boostorg/mysql/pull/328#issuecomment-2276206340  

## [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/328?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.71%. Comparing base [(`36dc476`)](https://app.codecov.io/gh/boostorg/mysql/commit/36dc47693eb9894889fb3b7ca0e5079feed7c573?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`189608a`)](https://app.codecov.io/gh/boostorg/mysql/commit/189608aa56d73c7fe85779fece8c5c1ff84d0419?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/mysql/pull/328/graphs/tree.svg?width=650&height=150&src=pr&token=ajbbjm3OiO&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/mysql/pull/328?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #328      +/-   ##  
===========================================  
+ Coverage    98.69%   98.71%   +0.02%       
===========================================  
  Files          141      144       +3       
  Lines         7115     7150      +35       
===========================================  
+ Hits          7022     7058      +36       
+ Misses          93       92       -1       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/mysql/pull/328?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/mysql/any\_connection.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/328?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fany_connection.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9hbnlfY29ubmVjdGlvbi5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/connection\_pool.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/328?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fconnection_pool.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9jb25uZWN0aW9uX3Bvb2wuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/detail/connection\_impl.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/328?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fconnection_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvY29ubmVjdGlvbl9pbXBsLmhwcA==) | `95.00% <100.00%> (-0.24%)` | :arrow_down: |  
| [include/boost/mysql/detail/initiation\_base.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/328?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Finitiation_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvaW5pdGlhdGlvbl9iYXNlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [...nclude/boost/mysql/detail/intermediate\_handler.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/328?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fdetail%2Fintermediate_handler.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9kZXRhaWwvaW50ZXJtZWRpYXRlX2hhbmRsZXIuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/impl/with\_diagnostics.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/328?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fimpl%2Fwith_diagnostics.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC9pbXBsL3dpdGhfZGlhZ25vc3RpY3MuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/mysql/throw\_on\_error.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/328?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fthrow_on_error.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC90aHJvd19vbl9lcnJvci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/mysql/with\_diagnostics.hpp](https://app.codecov.io/gh/boostorg/mysql/pull/328?src=pr&el=tree&filepath=include%2Fboost%2Fmysql%2Fwith_diagnostics.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9teXNxbC93aXRoX2RpYWdub3N0aWNzLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/mysql/pull/328/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/mysql/pull/328?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/mysql/pull/328?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [36dc476...189608a](https://app.codecov.io/gh/boostorg/mysql/pull/328?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
