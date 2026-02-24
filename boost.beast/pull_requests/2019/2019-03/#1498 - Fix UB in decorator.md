# #1498 Fix UB in decorator: [Closed]

> Username: djarek  
> Created at: 2019-03-04 14:04:45 UTC  
> Updated at: 2019-09-10 21:03:53 UTC  
> Closed at: 2019-03-06 13:07:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1498  

- don't assume layout or size overhead of classes with  
  virtual members (use split vtable)  
- don't use SOO for types with throwing move

---

## Comment 1

> Username: djarek  
> Created_at: 2019-03-06 13:07:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1498#issuecomment-470100299  

Merged in 228.

---
