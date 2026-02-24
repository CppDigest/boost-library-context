# #42 Fix using signals2 with gcc 10 and --std=gnu++20: deprecated std::all… [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2020-03-09 16:55:15 UTC  
> Updated at: 2020-03-11 18:08:04 UTC  
> Merged at: 2020-03-11 18:08:03 UTC  
> Closed at: 2020-03-11 18:08:03 UTC  
> Url: https://github.com/boostorg/signals2/pull/42  

…ocator member access.  
  
In C++20, the member "pointer" has been removed. However it's still defined in std::allocator_traits.

---
