# #368 - Make websocket::stream allocator-aware [Closed]

> Username: vinniefalco  
> Created at: 2017-05-18 17:58:51 UTC  
> Updated at: 2019-04-19 02:05:55 UTC  
> Closed at: 2019-04-19 02:05:54 UTC  
> Url: https://github.com/boostorg/beast/issues/368  

Self explanatory

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-19 02:05:54 UTC  
> Url: https://github.com/boostorg/beast/issues/368#issuecomment-484744802  

No one is asking for this, there is no precedent for it in ASIO, and I am questioning the utility... I strive to make the composed operations not allocate memory at all, instead relying on the user-provided types (such as _DynamicBuffer_).
