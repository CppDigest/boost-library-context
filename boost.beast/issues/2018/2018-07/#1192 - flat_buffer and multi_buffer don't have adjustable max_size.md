# #1192 - flat_buffer and multi_buffer don't have adjustable max_size [Closed]

> Username: djarek  
> Created at: 2018-07-16 13:42:51 UTC  
> Updated at: 2019-01-13 02:51:58 UTC  
> Closed at: 2019-01-13 02:51:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1192  

These DynamicBuffers should have the capability to adjust the max_size after the object has been constructed. Useful e.g. when a connection has been authenticated, which means we trust the user to send more data.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-08-15 14:23:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1192#issuecomment-413213399  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-09-14 15:58:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1192#issuecomment-421404161  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-01-13 02:51:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1192#issuecomment-453797529  

This should be resolved now
