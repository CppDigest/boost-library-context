# #22  Account for character class being char or unsigned char. [Closed]

> Username: saturnjct  
> Created at: 2016-03-28 19:57:47 UTC  
> Updated at: 2023-11-17 14:14:52 UTC  
> Closed at: 2023-11-17 14:14:52 UTC  
> Url: https://github.com/boostorg/property_tree/pull/22  

Avoid comparing to <= 0xFF and triggering a GCC warning. See https://svn.boost.org/trac/boost/ticket/5598

---

## Comment 1

> Username: LRFLEW  
> Created_at: 2016-09-16 06:31:01 UTC  
> Url: https://github.com/boostorg/property_tree/pull/22#issuecomment-247528009  

Perhaps you should also add `signed char` to that, since it is considered a separate type from `char`.

---

## Comment 2

> Username: ashtum  
> Created_at: 2023-11-17 13:46:30 UTC  
> Url: https://github.com/boostorg/property_tree/pull/22#issuecomment-1816460917  

@pdimov, it seems that this issue existed in earlier versions of GCC. I was unable to replicate it using any compiler except GCC 4.1.2: https://godbolt.org/z/W4e6e5hdT  
Considering that create_escapes is a templated function, I assume that receiving no warning is the expected behavior which has been fixed in later versions of GCC.  
Do you think this is still worth fixing?

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-11-17 14:09:50 UTC  
> Url: https://github.com/boostorg/property_tree/pull/22#issuecomment-1816498115  

Link to ticket for reference: http://trac.cpp.al/trac10/ticket/5598

---

## Comment 4

> Username: pdimov  
> Created_at: 2023-11-17 14:11:14 UTC  
> Url: https://github.com/boostorg/property_tree/pull/22#issuecomment-1816500223  

No, I don't think we need to care about GCC 4.1 at this point. The minimum supported version in Boost today is 4.8.

---
