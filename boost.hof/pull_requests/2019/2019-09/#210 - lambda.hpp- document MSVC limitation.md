# #210 lambda.hpp: document MSVC limitation [Merged]

> Username: CJBussey  
> Created at: 2019-09-10 17:18:17 UTC  
> Updated at: 2020-05-20 17:50:20 UTC  
> Merged at: 2020-05-20 17:50:20 UTC  
> Closed at: 2020-05-20 17:50:20 UTC  
> Url: https://github.com/boostorg/hof/pull/210  

* Since the MSVC implementation doesn't use a static variable to allocate memory for the lambda, the pre-C++17 implementation doesn't guarantee a unique address for MSVC.  
* This is now documented in the description.

---
