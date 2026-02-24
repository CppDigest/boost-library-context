# #99 Fix crashes with RTTI-off on MSVC [Closed]

> Username: MarcelRaad  
> Created at: 2016-04-19 13:21:48 UTC  
> Updated at: 2016-05-27 08:41:20 UTC  
> Closed at: 2016-05-27 08:40:44 UTC  
> Url: https://github.com/boostorg/test/pull/99  

When RTTI is off on MSVC, BOOST_NO_TYPEID is not defined,  
but only static typeid works. Dynamic typeid crashes at runtime.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2016-05-23 22:11:16 UTC  
> Url: https://github.com/boostorg/test/pull/99#issuecomment-221111482  

Thanks, currently under testing. Would you mind creating a ticket on trac?

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2016-05-24 07:12:57 UTC  
> Url: https://github.com/boostorg/test/pull/99#issuecomment-221185242  

Thanks! I've created a ticket here:  
https://svn.boost.org/trac/boost/ticket/12224

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2016-05-27 08:40:44 UTC  
> Url: https://github.com/boostorg/test/pull/99#issuecomment-222092537  

Thanks, under testing, should be in 1.62. I am closing the PR.

---
