# #357 - Flat tree: reserve generates dangling pointers [Closed]

> Username: anarthal  
> Created at: 2025-11-20 12:52:25 UTC  
> Updated at: 2025-11-29 20:35:55 UTC  
> Closed at: 2025-11-29 20:35:55 UTC  
> Url: https://github.com/boostorg/redis/issues/357  

A call to `reserve` that causes a reallocation in the data buffer leaves any views dangling. This is not as bad as it sounds because `reserve` is usually called before inserting nodes.  
  
I will try to fix this with the other reported problems in a PR.
