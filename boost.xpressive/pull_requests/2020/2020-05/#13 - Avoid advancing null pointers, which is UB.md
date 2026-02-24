# #13 Avoid advancing null pointers, which is UB [Merged]

> Username: Lastique  
> Created at: 2020-05-07 12:34:26 UTC  
> Updated at: 2020-07-14 21:15:35 UTC  
> Merged at: 2020-05-10 19:41:16 UTC  
> Closed at: 2020-05-10 19:41:16 UTC  
> Url: https://github.com/boostorg/xpressive/pull/13  

Advancing a null pointer by a distance other than 0 is UB in C++20 (7.6.6 Additive operators [expr.add]/4).  
  
This issue is reported by clang 10 UBSan here:  
  
https://travis-ci.org/github/boostorg/log/jobs/683550541#L1204  
  
Also, add a missing include for `std::size_t`.

---

## Comment 1

> Username: ericniebler  
> Created_at: 2020-05-10 19:41:21 UTC  
> Url: https://github.com/boostorg/xpressive/pull/13#issuecomment-626379019  

Thanks.

---

## Comment 2

> Username: Lastique  
> Created_at: 2020-06-23 19:32:00 UTC  
> Url: https://github.com/boostorg/xpressive/pull/13#issuecomment-648373384  

Please, merge to master.

---

## Comment 3

> Username: Lastique  
> Created_at: 2020-07-14 21:15:34 UTC  
> Url: https://github.com/boostorg/xpressive/pull/13#issuecomment-658417551  

Ping? The 1.74 release is approaching.

---
