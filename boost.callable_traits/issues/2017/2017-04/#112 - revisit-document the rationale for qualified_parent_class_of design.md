# #112 - [review] revisit/document the rationale for qualified_parent_class_of design [Open]

> Username: badair  
> Created at: 2017-04-09 03:35:06 UTC  
> Updated at: 2017-04-17 04:12:17 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/112  

from http://lists.boost.org/Archives/boost/2017/04/234173.php

---

## Comment 1

> Username: ldionne  
> Created at: 2017-04-17 04:12:17 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/112#issuecomment-294401721  

Also seen in https://lists.boost.org/Archives/boost/2017/04/234333.php:  
  
> 1. Why does `qualified_parent_class_of` return a ref qualified type even for unqualified PMFs? This is again surprising, I would expect `qualified_parent_class_of_t<void (foo::*)()>` to return `foo`, not `foo&`.  
> 2. Why does `qualified_parent_class_of` always return a `const&` qualified type for PMDs? I can imagine that this would be quite an annoyance if the user wants to apply custom qualifiers to it for one reason or another, especially rvalue reference, which would be silently collapsed away by the lvalue reference. The safest choice IMO would be to return an unqualified type.
