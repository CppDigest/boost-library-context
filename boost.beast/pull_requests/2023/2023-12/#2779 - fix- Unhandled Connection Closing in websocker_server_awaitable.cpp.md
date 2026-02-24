# #2779 fix: Unhandled Connection Closing in websocker_server_awaitable.cpp [Merged]

> Username: Anefu  
> Created at: 2023-12-23 07:50:45 UTC  
> Updated at: 2023-12-24 06:18:51 UTC  
> Merged at: 2023-12-24 06:18:50 UTC  
> Closed at: 2023-12-24 06:18:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2779  

This PR fixes #2778

---

## Comment 1

> Username: ashtum  
> Created_at: 2023-12-23 08:31:47 UTC  
> Updated_at: 2023-12-23 08:34:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2779#issuecomment-1868241674  

Thank you for the PR,  
The problem seems to be in this line: https://github.com/boostorg/beast/blob/192b7d5a43a7a95dd29df8b08cff635fb14ad9a6/example/websocket/server/awaitable/websocket_server_awaitable.cpp#L81  
It continues to loop if `ec == websocket::error::closed` which leads to an operation_aborted error. I would suggest putting the  whole loop inside a try-catch block so it will break out of the loop if an exception is thrown, something like:  
```C++  
    try  
    {  
        for(;;)  
        {  
            // This buffer will hold the incoming message  
            beast::flat_buffer buffer;  
  
            // Read a message  
            co_await ws.async_read(buffer);  
  
            // Echo the message back  
            ws.text(ws.got_text());  
            co_await ws.async_write(buffer.data());  
        }  
    }  
    catch(const boost::system::system_error & se)  
    {  
        if (se.code() != websocket::error::closed)  
            throw;  
    }  
```  
And check if exception_ptr is not null (as your current code does).

---

## Comment 2

> Username: Anefu  
> Created_at: 2023-12-23 13:33:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2779#issuecomment-1868295815  

Thanks for the suggestion.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-12-23 14:30:54 UTC  
> Updated_at: 2023-12-23 14:51:01 UTC  
> Url: https://github.com/boostorg/beast/pull/2779#issuecomment-1868305852  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2779?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`192b7d5`)](https://app.codecov.io/gh/boostorg/beast/commit/192b7d5a43a7a95dd29df8b08cff635fb14ad9a6?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.01% compared to head [(`be9a9c1`)](https://app.codecov.io/gh/boostorg/beast/pull/2779?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 92.98%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2779/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2779?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2779      +/-   ##  
===========================================  
- Coverage    93.01%   92.98%   -0.03%       
===========================================  
  Files          178      178                
  Lines        13688    13688                
===========================================  
- Hits         12732    12728       -4       
- Misses         956      960       +4       
```  
  
  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2779/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2779?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2779?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [192b7d5...be9a9c1](https://app.codecov.io/gh/boostorg/beast/pull/2779?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
