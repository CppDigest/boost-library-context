# #224 - [FR] Ability to choose underlying containers for flat maps + extended allocators support [Open]

> Username: psiha  
> Created at: 2023-12-21 12:57:09 UTC  
> Updated at: 2023-12-21 12:57:09 UTC  
> Url: https://github.com/boostorg/unordered/issues/224  

Similarily to the way Boost.Container does it:  
https://www.boost.org/doc/libs/1_84_0/doc/html/container/extended_allocators.html  
[adopt_sequence](https://www.boost.org/doc/libs/1_84_0/doc/html/boost/container/flat_set.html#idm13126-bb)  
  
At the same time please try to accommodate "unusual backing storage": support using memory mapped, persistent, file backed storage - https://github.com/boostorg/container/issues/260.
