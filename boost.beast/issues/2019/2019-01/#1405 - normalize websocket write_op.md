# #1405 - normalize websocket write_op [Closed]

> Username: vinniefalco  
> Created at: 2019-01-06 03:59:23 UTC  
> Updated at: 2022-06-16 16:35:36 UTC  
> Closed at: 2022-06-16 16:35:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1405  

...to not need `handler_ptr`: find an unused part of the stream object to put the close frame so we don't need the extra allocation.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-02-05 20:01:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1405#issuecomment-460782603  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: stale[bot]  
> Created at: 2019-03-07 20:27:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1405#issuecomment-470681296  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-06-16 16:35:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1405#issuecomment-1157887040  

I don't see how this is possible, and it isn't really needed because the allocation rarely happens
