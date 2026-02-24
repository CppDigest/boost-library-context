# #3 Fixed strict aliasing violation. [Closed]

> Username: aligature  
> Created at: 2014-12-19 14:11:01 UTC  
> Updated at: 2015-01-05 19:40:04 UTC  
> Closed at: 2015-01-05 19:35:14 UTC  
> Url: https://github.com/boostorg/functional/pull/3  

Changed C-style cast and dereference to std::memcpy.  Exactly mirrors other code already in the file.

---

## Comment 1

> Username: danieljames  
> Created_at: 2015-01-05 19:35:14 UTC  
> Url: https://github.com/boostorg/functional/pull/3#issuecomment-68762814  

I'm going to merge this by rebasing onto develop, so it won't appear here. Although I don't really like the existing code much, someone else wrote it. I'm not sure if there's a better way though. I suppose std::memcpy can have a compiler specific implementation if it needs to, so it can solve problems that we can't.

---

## Comment 2

> Username: danieljames  
> Created_at: 2015-01-05 19:40:04 UTC  
> Url: https://github.com/boostorg/functional/pull/3#issuecomment-68763598  

It's in commit d5dc5033d24d98ab19a24686f0a67cad7645c5fb

---
