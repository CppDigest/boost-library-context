# #313 - Client should close with 1010 if permessage-deflate offer is rejected [Open]

> Username: vinniefalco  
> Created at: 2017-04-21 18:03:21 UTC  
> Updated at: 2021-12-03 15:44:32 UTC  
> Url: https://github.com/boostorg/beast/issues/313  

When the client configuration requires the permessage-deflate extension and the server does not offer it in the response, the client should perform the WebSocket Close with 1010 (`close_code::needs_extension`).

---

## Comment 1

> Username: badgujarsachin83  
> Created at: 2021-12-02 18:03:46 UTC  
> Url: https://github.com/boostorg/beast/issues/313#issuecomment-984868713  

i want to work on this issue .can you tell you breifly how to solve this issue.

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-12-03 15:44:32 UTC  
> Url: https://github.com/boostorg/beast/issues/313#issuecomment-985623726  

I think a good start would be to set up a test that sets up the above scenario. Then place a breakpoint in the code for the async operation for `async_handshake` and the sync operation for `handshake`.
