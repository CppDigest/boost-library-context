# #293 [type] Add hana::typeid_ and deprecate hana::decltype_ [Merged]

> Username: ldionne  
> Created at: 2016-07-08 01:03:38 UTC  
> Updated at: 2016-07-18 15:13:18 UTC  
> Merged at: 2016-07-18 05:50:21 UTC  
> Closed at: 2016-07-18 05:50:21 UTC  
> Url: https://github.com/boostorg/hana/pull/293  

Candidate for satisfying #290. I'm still unsure whether using `typeof` would be better, though.

---

## Comment 1

> Username: viboes  
> Created_at: 2016-07-08 06:14:50 UTC  
> Updated_at: 2016-07-18 00:29:09 UTC  
> Url: https://github.com/boostorg/hana/pull/293#discussion_r70029238  

s/are can/can/

---

## Comment 2

> Username: ldionne  
> Created_at: 2016-07-08 14:23:32 UTC  
> Updated_at: 2016-07-18 00:29:09 UTC  
> Url: https://github.com/boostorg/hana/pull/293#discussion_r70081638  

Thanks!

---

## Comment 3

> Username: ricejasonf  
> Created_at: 2016-07-08 15:46:52 UTC  
> Url: https://github.com/boostorg/hana/pull/293#issuecomment-231395605  

`typeof` :+1:

---

## Comment 4

> Username: ldionne  
> Created_at: 2016-07-17 21:21:36 UTC  
> Url: https://github.com/boostorg/hana/pull/293#issuecomment-233204894  

I decided against `typeof`, even though I prefer it, because it clashes with a GCC extension of the same name. When compiling with `std=gnu++14`, using `typeof` anywhere in your program causes a compilation error.

---
