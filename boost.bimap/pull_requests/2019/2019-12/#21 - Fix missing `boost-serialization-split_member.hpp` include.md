# #21 Fix missing `boost/serialization/split_member.hpp` include [Merged]

> Username: SoapGentoo  
> Created at: 2019-12-21 13:06:36 UTC  
> Updated at: 2019-12-21 16:14:15 UTC  
> Merged at: 2019-12-21 16:13:58 UTC  
> Closed at: 2019-12-21 16:13:58 UTC  
> Url: https://github.com/boostorg/bimap/pull/21  

* As suggested by @glenfe and @robertramey in  
  https://github.com/boostorg/serialization/commit/c32a663c9963385430abc563f9c85f94d8da43a9#r36528430,  
  the `boost/serialization/split_member.hpp` include is not part  
  of the interface and consumers should not rely on it being  
  included.  
  
Fixes lightspark/lightspark#406  
Fixes https://bugs.gentoo.org/703294

---

## Comment 1

> Username: SoapGentoo  
> Created_at: 2019-12-21 13:06:59 UTC  
> Url: https://github.com/boostorg/bimap/pull/21#issuecomment-568179968  

@glenfe could you merge this, seeing you were the last person to touch bimap?

---
