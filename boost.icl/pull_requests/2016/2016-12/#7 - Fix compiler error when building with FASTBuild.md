# #7 Fix compiler error when building with FASTBuild [Merged]

> Username: nre-ableton  
> Created at: 2016-12-22 14:25:43 UTC  
> Updated at: 2017-11-21 22:46:36 UTC  
> Merged at: 2017-11-21 22:46:36 UTC  
> Closed at: 2017-11-21 22:46:36 UTC  
> Url: https://github.com/boostorg/icl/pull/7  

FASTBuild attempts to preprocess and cache `element_iterator.hpp`, but the missing space between the `)` and the `const` tokens cause the compiler to throw error [C2065][1]: `'Intervalconst': undeclared identifier`  
  
  
[1]: https://msdn.microsoft.com/en-us/library/ewcf0002.aspx

---
