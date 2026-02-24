# #17 - Timeouts [Closed]

> Username: anarthal  
> Created at: 2020-11-29 17:32:44 UTC  
> Updated at: 2024-08-10 16:16:03 UTC  
> Closed at: 2024-08-10 16:16:03 UTC  
> Url: https://github.com/boostorg/mysql/issues/17  

Provide a way to configure timeouts for the network operations. Consider whether to make it a connection-wide option or a per-operation option.  
  
Should be achievable using Asio's new cancellation slot mechanism. We should check how to leave connections in a well-defined state after cancellation (e.g. discarding any non-read frames before the next read).

---

## Comment 1

> Username: anarthal  
> Created at: 2024-08-10 16:16:03 UTC  
> Url: https://github.com/boostorg/mysql/issues/17#issuecomment-2282198329  

Asio already offers a built-in `cancel_after` token that makes this trivial. We've taken care in #331 to make everything compatible with it.
