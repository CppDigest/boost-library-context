# #112 Add missing template args to unordered_map<> serialization [Closed]

> Username: res2k  
> Created at: 2018-07-31 10:53:02 UTC  
> Updated at: 2018-11-14 08:39:24 UTC  
> Closed at: 2018-07-31 15:17:28 UTC  
> Url: https://github.com/boostorg/serialization/pull/112  

Both `std::unordered_map<>` and `boost::unordered_map<>` miss the template argument for the value.  
Probably went unnoticed as this would only surface with a non-standard allocator.

---

## Comment 1

> Username: robertramey  
> Created_at: 2018-07-31 15:17:21 UTC  
> Url: https://github.com/boostorg/serialization/pull/112#issuecomment-409258715  

This looks like a no brainer.  I'm merging into develop now.  Subject to subsequent testing and merge to master.

---

## Comment 2

> Username: res2k  
> Created_at: 2018-11-14 08:39:24 UTC  
> Url: https://github.com/boostorg/serialization/pull/112#issuecomment-438581434  

> This looks like a no brainer. I'm merging into develop now. Subject to subsequent testing and merge to master.  
  
Ping?  
Despite your statement I didn't see these changes making it to develop branch yet.

---
