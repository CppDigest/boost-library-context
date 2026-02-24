# #9 Fix compilation with BOOST_NO_ANSI_APIS [Merged]

> Username: MarcelRaad  
> Created at: 2015-09-18 13:21:42 UTC  
> Updated at: 2016-09-19 16:22:13 UTC  
> Merged at: 2016-09-15 04:02:08 UTC  
> Closed at: 2016-09-15 04:02:08 UTC  
> Url: https://github.com/boostorg/uuid/pull/9  

With BOOST_NO_ANSI_APIS, there is no CryptAcquireContextA.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-09-18 16:35:49 UTC  
> Url: https://github.com/boostorg/uuid/pull/9#issuecomment-141501749  

I have no opinion on this change. It looks simple and straightforward, but I can't say about correctness (not knowing much about windows programming)

---

## Comment 2

> Username: Lastique  
> Created_at: 2015-09-20 14:22:20 UTC  
> Url: https://github.com/boostorg/uuid/pull/9#issuecomment-141792089  

It does look incorrect because the `crypt_acquire_context` call is ambiguous when `BOOST_NO_ANSI_APIS` is not defined.

---

## Comment 3

> Username: MarcelRaad  
> Created_at: 2015-09-20 19:48:42 UTC  
> Url: https://github.com/boostorg/uuid/pull/9#issuecomment-141827036  

Thanks @Lastique, fixed now. This is the version I had originally against Boost 1.59, but I thought I could avoid the ifdef with the recent winapi develop changes and forgot to re-test without  BOOST_NO_ANSI_APIS :-(

---

## Comment 4

> Username: Lastique  
> Created_at: 2015-09-20 21:45:22 UTC  
> Url: https://github.com/boostorg/uuid/pull/9#issuecomment-141837763  

You can avoid the preprocessor check if you just always call `CryptAcquireContextW`.

---

## Comment 5

> Username: MarcelRaad  
> Created_at: 2015-09-21 07:04:28 UTC  
> Url: https://github.com/boostorg/uuid/pull/9#issuecomment-141895302  

Ah, of course, thanks!

---

## Comment 6

> Username: MarcelRaad  
> Created_at: 2016-02-11 12:28:18 UTC  
> Url: https://github.com/boostorg/uuid/pull/9#issuecomment-182836416  

Ping?

---

## Comment 7

> Username: MarcelRaad  
> Created_at: 2016-09-19 16:22:13 UTC  
> Url: https://github.com/boostorg/uuid/pull/9#issuecomment-248041816  

Thanks!

---
