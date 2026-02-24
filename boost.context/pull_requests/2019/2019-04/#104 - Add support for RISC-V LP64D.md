# #104 Add support for RISC-V LP64D [Merged]

> Username: andreas-schwab  
> Created at: 2019-04-09 12:32:27 UTC  
> Updated at: 2019-04-09 13:09:09 UTC  
> Merged at: 2019-04-09 13:09:09 UTC  
> Closed at: 2019-04-09 13:09:09 UTC  
> Url: https://github.com/boostorg/context/pull/104  



---

## Comment 1

> Username: olk  
> Created_at: 2019-04-09 12:51:17 UTC  
> Url: https://github.com/boostorg/context/pull/104#issuecomment-481237764  

looks good - did you execute the unit-tests (sub-directory test)?

---

## Comment 2

> Username: andreas-schwab  
> Created_at: 2019-04-09 13:04:55 UTC  
> Url: https://github.com/boostorg/context/pull/104#issuecomment-481242711  

Yes, I ran the tests in libs/context and libs/coroutine.  The only failures were the tests requiring -fsplit-stack which isn't supported.

---

## Comment 3

> Username: olk  
> Created_at: 2019-04-09 13:09:04 UTC  
> Url: https://github.com/boostorg/context/pull/104#issuecomment-481244168  

very nice - thank you!

---
