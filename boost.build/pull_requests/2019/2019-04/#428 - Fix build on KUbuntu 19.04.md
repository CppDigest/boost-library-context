# #428 Fix build on KUbuntu 19.04 [Closed]

> Username: djarek  
> Created at: 2019-04-15 01:40:22 UTC  
> Updated at: 2021-10-02 21:19:07 UTC  
> Closed at: 2019-04-15 01:48:00 UTC  
> Url: https://github.com/boostorg/build/pull/428  

Bootstrap fails on 19.04 because of references to non-existent `*.c` files, even though building `b2` succeeds.

---

## Comment 1

> Username: djarek  
> Created_at: 2019-04-15 01:48:00 UTC  
> Url: https://github.com/boostorg/build/pull/428#issuecomment-483080984  

The file got removed, it's not working without it anyway.

---
