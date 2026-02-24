# #2913 Parser.consume [Open]

> Username: klemens-morgenstern  
> Created at: 2024-08-01 16:29:44 UTC  
> Updated at: 2025-05-26 22:40:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2913  

I want to write a stream API for http.   
  
I will also need a buffer to reset a previously returned buffer from a body-writer.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2024-08-01 16:37:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2913#pullrequestreview-2213333482  

📁 include/boost/beast/http/write.hpp

```diff
 102 | 
 103 |-     @return The number of bytes written to the stream.
 103 |+     @return The number of bytes read from the body.
```

> Username: vinniefalco  
> Created_at: 2024-08-01 16:37:06 UTC  
> Updated_at: 2024-08-01 16:37:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2913#discussion_r1700494036  

This is a breaking API change...

> Username: klemens-morgenstern  
> Created_at: 2024-08-01 16:56:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2913#discussion_r1700515844  

Yes, and one that is overdue.

> Username: vinniefalco  
> Created_at: 2024-08-02 10:51:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2913#discussion_r1701687417  

The meaning of the return value used to be different from what it is now. We changed it because of user requests. If we change it again, then we are breaking those users.

> Username: ashtum  
> Created_at: 2024-08-02 12:38:36 UTC  
> Updated_at: 2024-08-02 12:38:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2913#discussion_r1701792381  

> The meaning of the return value used to be different from what it is now. We changed it because of user requests. If we change it again, then we are breaking those users.  
  
Wasn't that for `async_read_some`? https://github.com/boostorg/beast/issues/1942

> Username: vinniefalco  
> Created_at: 2024-08-02 19:50:29 UTC  
> Updated_at: 2024-08-02 19:50:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2913#discussion_r1702261684  

Maybe. I think you should decide if this is a good change. And if not, what else we might do (if anything).


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-08-01 23:33:24 UTC  
> Updated_at: 2025-05-26 22:40:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2913#issuecomment-2264205930  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2913?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `97.59036%` with `2 lines` in your changes missing coverage. Please review.  
> Project coverage is 93.08%. Comparing base [(`fee9be0`)](https://app.codecov.io/gh/boostorg/beast/commit/fee9be0be10c9c9a22ac1505a710d1d8ed5a3dfb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`83725e0`)](https://app.codecov.io/gh/boostorg/beast/commit/83725e0efa2a313cf64955eacce7e50cb2835e62?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 62 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/beast/pull/2913?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/beast/http/impl/serializer.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2913?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Fserializer.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5ocHA=) | 97.22% | [2 Missing :warning: ](https://app.codecov.io/gh/boostorg/beast/pull/2913?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2913/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2913?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2913      +/-   ##  
===========================================  
- Coverage    93.09%   93.08%   -0.02%       
===========================================  
  Files          177      177                
  Lines        13709    13773      +64       
===========================================  
+ Hits         12763    12821      +58       
- Misses         946      952       +6       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/beast/pull/2913?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/buffers\_cat.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2913?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fbuffers_cat.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2J1ZmZlcnNfY2F0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/core/buffers\_suffix.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2913?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fcore%2Fbuffers_suffix.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2J1ZmZlcnNfc3VmZml4LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/http/impl/write.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2913?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Fwrite.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaHBw) | `92.13% <100.00%> (+0.06%)` | :arrow_up: |  
| [include/boost/beast/http/serializer.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2913?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fserializer.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `90.90% <ø> (ø)` | |  
| [include/boost/beast/http/impl/serializer.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2913?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fhttp%2Fimpl%2Fserializer.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5ocHA=) | `91.48% <97.22%> (+1.13%)` | :arrow_up: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2913/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2913?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2913?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [fee9be0...83725e0](https://app.codecov.io/gh/boostorg/beast/pull/2913?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
