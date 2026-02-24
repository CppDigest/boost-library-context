# #768 - consuming_buffers specializations [Closed]

> Username: vinniefalco  
> Created at: 2017-09-07 14:43:41 UTC  
> Updated at: 2018-06-28 18:01:16 UTC  
> Closed at: 2018-06-28 18:01:16 UTC  
> Url: https://github.com/boostorg/beast/issues/768  

`consuming_buffers` could have specializations to optimize the common cases such as 1 buffer or `std::array` of 2 buffers. This is the approach used in asio.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-04-24 17:52:20 UTC  
> Url: https://github.com/boostorg/beast/issues/768#issuecomment-384022532  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-04-29 15:29:37 UTC  
> Url: https://github.com/boostorg/beast/issues/768#issuecomment-385259452  

This could be helpful but only after analyzing all current uses of `consuming_buffers` in the library

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-05-29 16:14:19 UTC  
> Url: https://github.com/boostorg/beast/issues/768#issuecomment-392836863  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-06-28 17:38:25 UTC  
> Url: https://github.com/boostorg/beast/issues/768#issuecomment-401115482  

This issue has been open for a while with no activity, has it been resolved?
