# #611 - Possible incorrect code dependency on std::pmr::things [Closed]

> Username: beached  
> Created at: 2021-08-18 06:30:47 UTC  
> Updated at: 2024-10-09 15:44:40 UTC  
> Closed at: 2024-10-09 15:44:40 UTC  
> Url: https://github.com/boostorg/json/issues/611  

When reporting a bug please include the following:  
  
### Version of Boost  
origin/develop commit 3f48a274589aac06280b2c1b0e3d70796cac4b05  
  
  
These break when std::experimental::polymorphic_allocator<value> is in use in standalone mode  
https://github.com/boostorg/json/blob/develop/test/memory_resource.cpp#L62 is depending on `std::pmr::polymorphic_allocator<value>` instead of `polymorphic_allocator<value>` using the existing alias.    
  
https://github.com/boostorg/json/blob/develop/test/doc_background.cpp#L33  
is using `std::pmr::vector<T>` instead of `std::vector<T, polymorphic_allocator<T>>`  
  
### All relevant compiler information  
Macos 11.5.1/Apple clang version 12.0.5 (clang-1205.0.22.9)

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-10-09 15:44:40 UTC  
> Url: https://github.com/boostorg/json/issues/611#issuecomment-2402694284  

Fixed at some point in the past.
