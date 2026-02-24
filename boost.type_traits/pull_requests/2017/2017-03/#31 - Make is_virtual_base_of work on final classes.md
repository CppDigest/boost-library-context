# #31 Make is_virtual_base_of work on final classes [Closed]

> Username: csernib  
> Created at: 2017-03-29 21:06:06 UTC  
> Updated at: 2018-05-10 07:49:14 UTC  
> Closed at: 2018-05-10 07:49:14 UTC  
> Url: https://github.com/boostorg/type_traits/pull/31  

The current `boost::is_virtual_base_of<Base, Derived>` metaprogram uses inheritance and size comparision when finding out whether `Base` is a virtual base of `Derived`. This however fails to compile if `Derived` is defined final.  
  
This redesign of `boost::is_virtual_base_of` uses the standard's rules of pointer to pointer conversion to determine the output, which also works on final classes and is less dependant on specific compiler implementations.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-05-10 07:49:14 UTC  
> Url: https://github.com/boostorg/type_traits/pull/31#issuecomment-387979846  

Since there's no CI on this, closed in favour of https://github.com/boostorg/type_traits/pull/71: please see comments therein.

---
