# #1076 - object::operator[] documentation wording on reference stability [Closed]

> Username: DeuceBox  
> Created at: 2025-02-27 04:35:29 UTC  
> Updated at: 2025-02-27 12:45:54 UTC  
> Closed at: 2025-02-27 12:45:54 UTC  
> Url: https://github.com/boostorg/json/issues/1076  

From: https://live.boost.org/doc/libs/1_87_0/libs/json/doc/html/json/ref/boost__json__object/operator__lb__rb_.html  
>If an insertion occurs and results in a rehashing of the container, all iterators are invalidated. Otherwise iterators are not affected. References are not invalidated.  
  
I initially interpreted that as references are not invalidated as a result of rehashing. Should that actually read "If an insertion occurs and results in a rehashing of the container, all iterators and references are invalidated. Otherwise iterators are not effected and references are not invalidated."  
  
That's what the big warning here https://live.boost.org/doc/libs/1_87_0/libs/json/doc/html/json/dom/object.html (which I saw afterwards) implies:  
> Unlike traditional node based containers like std::set, there is no guarantee of reference stability or iterator stability on insertions and erasures.

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-02-27 08:24:34 UTC  
> Url: https://github.com/boostorg/json/issues/1076#issuecomment-2687228818  

Oh, this appears to be an error in the documentation from the earliest days when `object` was node-based. The references will in fact be invalidated together with iterators.
