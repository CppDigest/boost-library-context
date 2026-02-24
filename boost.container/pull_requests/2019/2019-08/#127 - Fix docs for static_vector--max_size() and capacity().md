# #127 Fix docs for static_vector::max_size() and capacity() [Closed]

> Username: logan-5  
> Created at: 2019-08-19 18:04:30 UTC  
> Updated at: 2019-11-14 22:37:12 UTC  
> Closed at: 2019-11-14 22:36:39 UTC  
> Url: https://github.com/boostorg/container/pull/127  

The documentation for `static_vector::max_size()` and `static_vector::capacity()` list them as `static` member functions; however, they are actually implemented as non-static `const` member functions. This PR fixes this discrepancy by changing the documentation to match the implementation.  
  
The alternative would be to change the implementations to be `static` (which they could be, since for `static_vector<T, N>`, both simply return `N`). However, this is difficult due to the way `static_vector` is implemented (it inherits these member functions from `vector`), and besides, there already exists `static_vector::static_capacity` to accomplish the same thing statically.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2019-11-14 22:37:12 UTC  
> Url: https://github.com/boostorg/container/pull/127#issuecomment-554113863  

The intention was to make them static, so I've changed the implement to reflect what the documentation states. Thanks for the report!

---
