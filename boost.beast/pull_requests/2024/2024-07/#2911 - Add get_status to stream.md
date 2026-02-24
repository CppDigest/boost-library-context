# #2911 Add get_status to stream [Merged]

> Username: britasys  
> Created at: 2024-07-29 19:11:05 UTC  
> Updated at: 2024-09-01 14:21:21 UTC  
> Merged at: 2024-09-01 14:21:11 UTC  
> Closed at: 2024-09-01 14:21:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2911  

This commit addresses the requirements outlined in issue #228, enhancing the module's functionality and supporting better status tracking.  
  
This is my first commit to this repository. So, I need help.  
I didn't know where to put the structure.  
  
@ashtum

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2024-07-29 19:42:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2911#issuecomment-2256761746  

This needs a test, and to ensure all new lines have coverage.

---

## Comment 2

> Username: britasys  
> Created_at: 2024-07-31 20:10:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2911#issuecomment-2261360986  

I have added a test for the get_status function and made some modifications to the initial commit.

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2024-08-01 00:30:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2911#pullrequestreview-2211343189  

📁 include/boost/beast/websocket/stream.hpp

```diff
 415 |     void
 423 |-     get_status(permessage_deflate_status& status);
 416 |+     get_status(detail::permessage_deflate_status &status);
```

> Username: vinniefalco  
> Created_at: 2024-08-01 00:30:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2911#discussion_r1699242980  

`detail` symbols are not supposed to be in public APIs

> Username: britasys  
> Created_at: 2024-08-02 22:52:22 UTC  
> Url: https://github.com/boostorg/beast/pull/2911#discussion_r1702383342  

The 'permessage_deflate_status' structure has been relocated to 'stream.hpp' as @ashtum suggested.


---

## Comment 4

> Username: ashtum  
> Created_at: 2024-08-01 08:08:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2911#issuecomment-2262317731  

@britasys, please move the `struct permessage_deflate_status` to the top of `stream.hpp` and add Javadoc comments for the struct and its members. Initialize the members with default values to minimize the risk of undefined behavior in users' code. For reference, see how it's done for the `permessage_deflate` struct: https://github.com/boostorg/beast/blob/fee9be0be10c9c9a22ac1505a710d1d8ed5a3dfb/include/boost/beast/websocket/option.hpp#L32

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2024-08-02 22:57:01 UTC  
> Updated_at: 2024-09-01 14:21:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2911#issuecomment-2266236996  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2911?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.10%. Comparing base [(`fee9be0`)](https://app.codecov.io/gh/boostorg/beast/commit/fee9be0be10c9c9a22ac1505a710d1d8ed5a3dfb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`be9b9db`)](https://app.codecov.io/gh/boostorg/beast/commit/be9b9dbdadd9d8e9c711639001a1f48d2671ab67?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 12 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2911/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2911?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2911   +/-   ##  
========================================  
  Coverage    93.09%   93.10%             
========================================  
  Files          177      177             
  Lines        13709    13721   +12       
========================================  
+ Hits         12763    12775   +12       
  Misses         946      946             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/beast/pull/2911?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/detail/impl\_base.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2911?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fdetail%2Fimpl_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ltcGxfYmFzZS5ocHA=) | `86.43% <100.00%> (+0.42%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2911?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fimpl%2Fstream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW0uaHBw) | `75.00% <100.00%> (+1.47%)` | :arrow_up: |  
| [include/boost/beast/websocket/stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2911?src=pr&el=tree&filepath=include%2Fboost%2Fbeast%2Fwebsocket%2Fstream.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2911?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2911?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [fee9be0...be9b9db](https://app.codecov.io/gh/boostorg/beast/pull/2911?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 6 [Commented]

> Username: ashtum  
> Created_at: 2024-08-03 14:56:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2911#pullrequestreview-2217065319  

📁 include/boost/beast/websocket/impl/stream.hpp

```diff
 179 |+ void
 180 |+ stream<NextLayer, deflateSupported>::
 181 |+ get_status(permessage_deflate_status &status)
```

> Username: ashtum  
> Created_at: 2024-08-03 14:31:59 UTC  
> Updated_at: 2024-08-03 14:56:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2911#discussion_r1702792688  

`get_status` should be marked `const noexcept`.


📁 include/boost/beast/websocket/detail/impl_base.hpp

```diff
 348 |+ 
 349 |+     void
 350 |+     get_pmd_config(detail::pmd_offer &pmd)
```

> Username: ashtum  
> Created_at: 2024-08-03 14:46:39 UTC  
> Updated_at: 2024-08-03 14:56:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2911#discussion_r1702798233  

Please rename this to `get_config_pmd` to be consistent with `get_option_pmd`.

---

📁 include/boost/beast/websocket/detail/impl_base.hpp

```diff
 510 |+         pmd.client_max_window_bits = 0;
 511 |+         pmd.server_no_context_takeover = false;
 512 |+         pmd.client_no_context_takeover = false;
```

> Username: ashtum  
> Created_at: 2024-08-03 14:55:49 UTC  
> Updated_at: 2024-08-03 14:56:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2911#discussion_r1702802213  

You might want to change these to `pmd = {};` instead.


---

## Review 7 [Commented]

> Username: ashtum  
> Created_at: 2024-08-05 09:23:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2911#pullrequestreview-2218496654  

📁 include/boost/beast/websocket/stream.hpp

```diff
  47 |+ 
  48 |+     Used to check the status of the permessage-deflate extension after
  49 |+     the WebSocket handshake.
```

> Username: ashtum  
> Created_at: 2024-08-05 09:23:27 UTC  
> Updated_at: 2024-08-05 09:24:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2911#discussion_r1703821671  

Please move this note to the Javadoc of `stream::get_status` and squash all the commits in a single commit with a message like `Make permessage-deflate status queryable in websocket::stream`.

> Username: britasys  
> Created_at: 2024-08-05 16:35:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2911#discussion_r1704380054  

Thank you @ashtum


---

## Comment 8

> Username: britasys  
> Created_at: 2024-08-08 15:11:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2911#issuecomment-2276070725  

Hi, Do I have to do anything else?

---

## Comment 9

> Username: ashtum  
> Created_at: 2024-08-08 15:28:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2911#issuecomment-2276110595  

> Hi, Do I have to do anything else?  
  
No, everything looks good to me. I've delayed the merge until the Boost 1.86 release, just in case we need to push a last-minute hotfix to develop branch. This will be included in Beast in Boost 1.87. Thank you.

---
