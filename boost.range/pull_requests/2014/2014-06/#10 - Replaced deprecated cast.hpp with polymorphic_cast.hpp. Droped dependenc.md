# #10 Replaced deprecated cast.hpp with polymorphic_cast.hpp. Droped dependenc... [Merged]

> Username: apolukhin  
> Created at: 2014-06-09 09:23:35 UTC  
> Updated at: 2014-06-16 09:40:18 UTC  
> Merged at: 2014-06-16 09:40:18 UTC  
> Closed at: 2014-06-16 09:40:18 UTC  
> Url: https://github.com/boostorg/range/pull/10  

...y to the Numeric library  
  
Tested on GCC

---

## Comment 1

> Username: neilgroves  
> Created_at: 2014-06-09 22:12:27 UTC  
> Url: https://github.com/boostorg/range/pull/10#issuecomment-45551028  

I merged this and ran the tests which subsequently failed because polymorphic_cast.hpp was not found.  
  
I need the polymorphic_cast.hpp file to exist in both develop and master before I can update Boost.Range to use it.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2014-06-10 09:41:28 UTC  
> Url: https://github.com/boostorg/range/pull/10#issuecomment-45593421  

Merged latest changes of the Conversion library to master branch. polymorphic_cast.hpp now exists in all the branches.

---
