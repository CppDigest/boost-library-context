# #4 Update test_poisoned_macros.cpp [Merged]

> Username: jzmaddock  
> Created at: 2014-07-09 17:17:39 UTC  
> Updated at: 2014-07-09 17:20:47 UTC  
> Merged at: 2014-07-09 17:20:47 UTC  
> Closed at: 2014-07-09 17:20:47 UTC  
> Url: https://github.com/boostorg/pool/pull/4  

Current test fails inside /boost/core/demangle.hpp: not sure why that header is even included, but anyway this fix solves the issue while preserving the intent of the test.

---
