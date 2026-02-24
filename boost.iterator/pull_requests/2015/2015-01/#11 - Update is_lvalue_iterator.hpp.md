# #11 Update is_lvalue_iterator.hpp [Merged]

> Username: jzmaddock  
> Created at: 2015-01-21 16:55:00 UTC  
> Updated at: 2015-05-21 20:35:39 UTC  
> Merged at: 2015-05-21 20:35:38 UTC  
> Closed at: 2015-05-21 20:35:39 UTC  
> Url: https://github.com/boostorg/iterator/pull/11  

In the current type_traits rewrite, type_traits headers no long implicitly include mpl ones, so mpl/bool.hpp has to be explicitly included now.

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-01-21 20:41:56 UTC  
> Updated_at: 2015-01-22 08:48:26 UTC  
> Url: https://github.com/boostorg/iterator/pull/11#discussion_r23332492  

The other #includes use the <> syntax. Can the new one follow this?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2015-01-22 08:48:48 UTC  
> Url: https://github.com/boostorg/iterator/pull/11#issuecomment-70988587  

My bad, updated to use <>

---
