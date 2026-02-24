# #263 Make static_vector<T,N> trivially relocatable when T is trivially relocatable [Open]

> Username: Quuxplusone  
> Created at: 2023-12-21 20:29:35 UTC  
> Updated at: 2023-12-21 20:29:35 UTC  
> Url: https://github.com/boostorg/container/pull/263  

This PR definitely isn't ready for prime time, but I figured I might as well exploit Cunningham's Law here.  
  
The idea is that when the compiler supports `__cpp_impl_trivially_relocatable` (P1144's proposed feature-test macro), then we should define `-DBOOST_CONTAINER_ATTRIBUTE_TRIVIALLY_RELOCATABLE_IF(x)=[[trivially_relocatable(x)]]`. (And/or set it to a vendor-specific attribute if-and-when anyone supports a vendor-specific attribute; no mainstream compiler does, yet.)  
  
The big problem with this patch as it stands right now is that I have no idea where these macros are configured. We want to do the same thing that `BOOST_CONTAINER_ATTRIBUTE_NODISCARD` does; but that's not configured anywhere in this repo, either.  
  
I see that Boost.Container is just inheriting all its type-traits from Boost.Move, so probably `dtl::is_trivially_relocatable` belongs in Boost.Move, not here?  
  
Fixes #258.

---
