# #841 Remove warning disable warning 4800 [Closed]

> Username: SiliconA-Z  
> Created at: 2022-10-14 19:19:38 UTC  
> Updated at: 2022-10-18 00:52:11 UTC  
> Closed at: 2022-10-18 00:52:11 UTC  
> Url: https://github.com/boostorg/math/pull/841  

We should do a comparison with 0 instead of forcing to bool

---

## Comment 1

> Username: mborland  
> Created_at: 2022-10-14 21:12:30 UTC  
> Url: https://github.com/boostorg/math/pull/841#issuecomment-1279473392  

What does this change if the warnings are already suppressed? How did you generate/find these?

---

## Comment 2

> Username: SiliconA-Z  
> Created_at: 2022-10-17 15:18:22 UTC  
> Url: https://github.com/boostorg/math/pull/841#issuecomment-1281037461  

I found these while browsing the code. It is best to get a bool directly via comparison than a conversion. It is considered to be better C++

---

## Comment 3

> Username: mborland  
> Created_at: 2022-10-18 00:52:11 UTC  
> Url: https://github.com/boostorg/math/pull/841#issuecomment-1281673833  

Without a specific bug/error/warning I am going to close this. Generally we are looking for compelling reasons to change code because of our large user base. If you are looking for ways to contribute there is a feature wishlist  https://github.com/boostorg/math/issues/303, or scan through our other open issues.

---
