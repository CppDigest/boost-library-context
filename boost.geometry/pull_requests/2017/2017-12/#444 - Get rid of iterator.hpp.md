# #444 Get rid of iterator.hpp [Merged]

> Username: DanielaE  
> Created at: 2017-12-30 10:34:44 UTC  
> Updated at: 2018-01-14 07:02:56 UTC  
> Merged at: 2018-01-04 11:46:58 UTC  
> Closed at: 2018-01-04 11:46:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/444  

Boost's iterator.hpp is deprecated (just like std::iterator in C++17). It does nothing but pulling std::iterator into namespace boost and including standard headers 'iterator' and 'cstddef'. Therefore get rid of including iterator.hpp (it had no effect in Boost.Geometry anyway) and replace inheritance by lifting std::iterator's members into the derived class. Instantiating std::iterator is granted with lengthy warning messages by latest MSVC when compiling in C++17 mode.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2017-12-30 13:12:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/444#issuecomment-354545584  

Thanks. Did you test this with C++03?

---

## Comment 2

> Username: DanielaE  
> Created_at: 2017-12-30 13:18:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/444#issuecomment-354545890  

Nope, but I can spin-up a test-run with msvc 9 if you like. If only this wouldn't take hours to finish ...

---

## Comment 3

> Username: DanielaE  
> Created_at: 2017-12-30 16:46:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/444#issuecomment-354555687  

Ok, ran it. I can see no problem with this patch and C++03.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2018-01-04 11:46:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/444#issuecomment-355262789  

Thanks!

---
