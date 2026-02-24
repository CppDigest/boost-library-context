# #739 fixed compilation error when using clang 4.0.1 with -std=c++1z [Closed]

> Username: adrianimboden  
> Created at: 2017-08-14 12:50:57 UTC  
> Updated at: 2017-08-16 06:56:11 UTC  
> Closed at: 2017-08-16 06:56:11 UTC  
> Url: https://github.com/boostorg/beast/pull/739  



---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-08-14 12:55:56 UTC  
> Url: https://github.com/boostorg/beast/pull/739#issuecomment-322182551  

The reason I added the braces is because without them, they fail to compile on one of these configurations (I can't remember which one):  
http://www.boost.org/development/tests/develop/developer/beast.html  
  
I think the only good way to fix this to make it work on all the different compilers is to introduce local variables. I will work on a fix that uses this style.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-08-15 14:35:49 UTC  
> Url: https://github.com/boostorg/beast/pull/739#issuecomment-322486239  

Can you please see if this is resolved in **version 106**, which was merged to *develop*?

---

## Comment 3

> Username: adrianimboden  
> Created_at: 2017-08-16 06:56:07 UTC  
> Url: https://github.com/boostorg/beast/pull/739#issuecomment-322683208  

Yes, works perfectly

---
