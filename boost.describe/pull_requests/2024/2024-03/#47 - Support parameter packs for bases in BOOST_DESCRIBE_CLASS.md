# #47 Support parameter packs for bases in BOOST_DESCRIBE_CLASS [Merged]

> Username: qchateau  
> Created at: 2024-03-22 21:48:36 UTC  
> Updated at: 2024-10-13 22:51:28 UTC  
> Merged at: 2024-10-13 22:51:28 UTC  
> Closed at: 2024-10-13 22:51:28 UTC  
> Url: https://github.com/boostorg/describe/pull/47  



---

## Comment 1

> Username: qchateau  
> Created_at: 2024-03-22 21:55:44 UTC  
> Url: https://github.com/boostorg/describe/pull/47#issuecomment-2015992237  

It seems one of the MSVC versions is failing to compile in the CI, but that also seems unrelated to my patch ...

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-03-23 00:36:25 UTC  
> Url: https://github.com/boostorg/describe/pull/47#issuecomment-2016229927  

I'll take care of the MSVC failures.  
  
However, while this is an interesting simplification, I intended to add `name` to the base class descriptors, and this makes it impossible. I'll need to think about this as it seems we can have one or the other, but not both.

---

## Comment 3

> Username: qchateau  
> Created_at: 2024-03-23 10:55:56 UTC  
> Url: https://github.com/boostorg/describe/pull/47#issuecomment-2016449408  

The simplification is a side effect to me. As a user, having the ability to use boost describe on structs inheriting of a parameter pack is very important. I current hack my way around it, but I'd like to have native support.  
It's indeed not compatible with naming bases, but would you be using the same macros to name bases ? If not, it's probably possible to support both features independantly, you'll just not be able to name parameter packs.

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-03-23 17:56:12 UTC  
> Url: https://github.com/boostorg/describe/pull/47#issuecomment-2016561291  

> but would you be using the same macros to name bases ?  
  
There's no separate macro for bases, it's invoked as part of BOOST_DESCRIBE_STRUCT and BOOST_DESCRIBE_CLASS. So yes, it would be the same macro. At the moment it just doesn't capture the names (the way the member descriptors do.)  
  
Your approach has the additional benefit of supporting something like https://godbolt.org/z/xoTrP4sxT without workarounds. I'm still unclear on whether this outweighs having the names (as `typeid(T).name()` is less convenient and doesn't work well for all uses.)  
  
Maybe it would be possible to support both. I'll have to think about it some more.

---

## Comment 5

> Username: qchateau  
> Created_at: 2024-03-23 18:28:39 UTC  
> Url: https://github.com/boostorg/describe/pull/47#issuecomment-2016569219  

That said, the macros let you record the name of the classes that are described.  
And you also record their bases (which can also be described).  
So can't you already get the name of the bases via their own description?

---
