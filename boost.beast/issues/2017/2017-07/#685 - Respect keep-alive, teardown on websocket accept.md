# #685 - Respect keep-alive, teardown on websocket accept [Open]

> Username: vinniefalco  
> Created at: 2017-07-27 21:33:15 UTC  
> Updated at: 2018-04-27 00:32:04 UTC  
> Url: https://github.com/boostorg/beast/issues/685  

When websocket accept fails to switch protocols, if the Connection specifies close semantics then teardown should be invoked.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-04-24 17:52:28 UTC  
> Url: https://github.com/boostorg/beast/issues/685#issuecomment-384022582  

This issue has been open for a while with no activity, has it been resolved?
