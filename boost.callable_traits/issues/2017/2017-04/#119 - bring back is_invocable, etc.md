# #119 - [review] bring back is_invocable, etc [Closed]

> Username: badair  
> Created at: 2017-04-09 03:52:22 UTC  
> Updated at: 2017-06-24 19:20:32 UTC  
> Closed at: 2017-06-24 19:16:46 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/119  

http://lists.boost.org/Archives/boost/2017/04/234298.php

---

## Comment 1

> Username: badair  
> Created at: 2017-04-09 04:02:41 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/119#issuecomment-292762494  

also from @ricejasonf

---

## Comment 2

> Username: ricejasonf  
> Created at: 2017-04-09 04:39:29 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/119#issuecomment-292763681  

I think it is spelled `is_invocable` at least in the proposed standard.  
  
http://en.cppreference.com/w/cpp/types/is_invocable

---

## Comment 3

> Username: badair  
> Created at: 2017-04-09 05:12:06 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/119#issuecomment-292764666  

@ricejasonf Ah, thanks. I just misspelled it on the Boost mailing list too.

---

## Comment 4

> Username: badair  
> Created at: 2017-06-24 19:20:32 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/119#issuecomment-310860530  

I only added back is_invocable and is_invocable_r. I think the nothrow versions aren't worth the trouble for now.
