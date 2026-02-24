# #28 Document an emulation limitation of scoped enum [Merged]

> Username: Sawyer47  
> Created at: 2017-04-16 15:46:57 UTC  
> Updated at: 2019-04-23 00:00:27 UTC  
> Merged at: 2019-04-23 00:00:27 UTC  
> Closed at: 2019-04-23 00:00:27 UTC  
> Url: https://github.com/boostorg/core/pull/28  

I recently stumbled upon a case in which scoped enums have different behaviors in C++03 and C++11. I thought it would be a good idea to document this case.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-04-16 22:38:22 UTC  
> Url: https://github.com/boostorg/core/pull/28#issuecomment-294378347  

I wonder if we should consider this a bug instead.

---

## Comment 2

> Username: viboes  
> Created_at: 2017-04-16 23:15:57 UTC  
> Url: https://github.com/boostorg/core/pull/28#issuecomment-294379761  

Hi and thanks for reporting.  
  
I agree that it is not clear from the documentation that the type of `enum_scoped::a` is not `enum_scoped`, but `enum_scoped::type` which of course is an enumeration.   
  
Your patch is welcome, and I suggest it is merged as soon as boost 1.64 is delivered.  
  
It could be possible to define constants `a` and `b` of type `enum_scoped` (with a different macro syntax), but these constants couldn't be used in the switch/case.   
I don't know how to have enumerators and constants with the same name.   
I don't know which names should be used for them; a for the enumerator and a_ for the class instance?   
  
I agree the workaround `enum_scoped(enum_scoped::a)` is cumbersome, but it works.    
  
I've opted for this partial solution as the more convenient.  
  
I don't think this is a bug we can fix without adding other macros.

---

## Comment 3

> Username: HDembinski  
> Created_at: 2018-08-03 20:39:14 UTC  
> Url: https://github.com/boostorg/core/pull/28#issuecomment-410370123  

Hi, just passing by and wondering why the PR was not accepted after all?

---

## Comment 4

> Username: pdimov  
> Created_at: 2019-04-22 17:21:43 UTC  
> Url: https://github.com/boostorg/core/pull/28#issuecomment-485482990  

Good question. Any objections to merging it?

---
