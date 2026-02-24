# #204 - overlapped_ptr and custom object executors [Closed]

> Username: djarek  
> Created at: 2019-02-19 00:53:37 UTC  
> Updated at: 2019-03-02 13:35:32 UTC  
> Closed at: 2019-03-02 13:35:32 UTC  
> Url: https://github.com/boostorg/asio/issues/204  

It's not possible to construct an `overlapped_ptr` when dealing with type-erased or user-defined I/O executor types.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-19 00:53:59 UTC  
> Url: https://github.com/boostorg/asio/issues/204#issuecomment-464935931  

Confirmed

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-02-20 17:11:57 UTC  
> Url: https://github.com/boostorg/asio/issues/204#issuecomment-465670248  

See https://github.com/boostorg/asio/pull/205

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-02-21 04:58:44 UTC  
> Url: https://github.com/boostorg/asio/issues/204#issuecomment-465862293  

Fixed here: https://github.com/boostorg/asio/commit/035775462e5c32d0112175745978f3627e7ee059
