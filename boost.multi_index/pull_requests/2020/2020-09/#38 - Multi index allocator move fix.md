# #38 Multi index allocator move fix [Closed]

> Username: zabereer  
> Created at: 2020-09-29 06:30:04 UTC  
> Updated at: 2020-09-29 08:59:19 UTC  
> Closed at: 2020-09-29 08:59:18 UTC  
> Url: https://github.com/boostorg/multi_index/pull/38  

Relating to issue #37   
  
This adds checks to the unit test to show the issue and a potential fix to copy the allocator on multi_index_container move construction - like was done previously on older boost versions.  
The unit test checks verify when copying the allocator that the copy is not taken from a moved-from allocator.  
  
Copies are taken in a few places, for example https://github.com/boostorg/multi_index/blob/boost-1.74.0/include/boost/multi_index/random_access_index.hpp#L772 and https://github.com/boostorg/multi_index/blob/boost-1.74.0/include/boost/multi_index/hashed_index.hpp#L710   
  
The allocator is copied to the indexes anyway so maybe the additional copy does not matter?  
Or maybe the indexes should take a reference to the allocator instead of copy?

---

## Comment 1

> Username: zabereer  
> Created_at: 2020-09-29 08:59:18 UTC  
> Url: https://github.com/boostorg/multi_index/pull/38#issuecomment-700567329  

Not an issue as allocator moved from should be equal to allocator it was moved to. See issue #37 .

---
