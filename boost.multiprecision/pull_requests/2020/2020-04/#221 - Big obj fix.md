# #221 Big obj fix [Closed]

> Username: jzmaddock  
> Created at: 2020-04-18 10:03:03 UTC  
> Updated at: 2021-01-30 16:10:14 UTC  
> Closed at: 2020-04-18 17:56:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/221  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-04-18 15:22:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/221#issuecomment-615888933  

Somehow I've managed to make a mess of this PR and base it on another PR rather than develop :(

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-04-18 17:58:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/221#issuecomment-615913633  

I saw your error as well, but it was fixed by adding <debug-symbols>off as well as  -Wa,-mbig-obj, what was the command line that failed?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2020-04-18 18:00:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/221#issuecomment-615914218  

Closed in favour of less messed up: https://github.com/boostorg/multiprecision/pull/222

---
