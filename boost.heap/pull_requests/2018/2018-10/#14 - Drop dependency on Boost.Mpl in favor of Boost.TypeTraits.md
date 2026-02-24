# #14 Drop dependency on Boost.Mpl in favor of Boost.TypeTraits [Merged]

> Username: glenfe  
> Created at: 2018-10-28 20:33:28 UTC  
> Updated at: 2018-10-29 07:41:14 UTC  
> Merged at: 2018-10-29 07:41:14 UTC  
> Closed at: 2018-10-29 07:41:14 UTC  
> Url: https://github.com/boostorg/heap/pull/14  

Heap already depends on TypeTraits and this change just uses those facilities from TypeTraits (which are also now in the C++ standard library) instead of MPL.

---
