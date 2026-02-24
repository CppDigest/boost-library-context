# #382 Remove boost.core dependencies [Merged]

> Username: mborland  
> Created at: 2021-10-17 07:50:41 UTC  
> Updated at: 2021-11-22 18:31:22 UTC  
> Merged at: 2021-11-22 16:44:32 UTC  
> Closed at: 2021-11-22 16:44:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/382  



---

## Comment 1

> Username: mborland  
> Created_at: 2021-10-17 13:26:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/382#issuecomment-945123180  

@ckormanyos, @NAThompson, and @jzmaddock this should be good for review. The diffstat is quite large but the preponderance of the changes are to namespaces, or names of macros.

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2021-10-23 12:31:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/382#pullrequestreview-787388148  

📁 include/boost/multiprecision/detail/nvp.hpp

```diff
   1 |+ /////////////////////////////////////////////////////////////////////
   2 |+ //  Copyright 2002 Robert Ramey.
```

> Username: jzmaddock  
> Created_at: 2021-10-23 12:31:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/382#discussion_r734965286  

I don't believe we can replace boost::nvp with our own type and expect it to work.  
  
The good news is we don't need to: if we're in standalone mode, then there can be no possible interoperability with Boost.Serialization anyway.  So I believe we should remove this header, revert back to using real boost::nvp etc, and just #if out all the serialization code in standalone mode.  In a few cases (cpp_int for example), the serialization code is in a separate header anyway, so we can just put a #if round the #include and be done with it.  That will reduce the amount of code being parsed in standalone mode as well, which is a good thing IMO :)  
  
Apologies for the extra editing Matt - I really do appreciate what you're doing here!


---

## Comment 3

> Username: mborland  
> Created_at: 2021-11-22 15:55:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/382#issuecomment-975667795  

This is clean with the requested changes.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-11-22 16:44:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/382#issuecomment-975717586  

Thanks Matt!!

---
