# #25 Fix compilation with BOOST_USE_WINDOWS_H [Merged]

> Username: MarcelRaad  
> Created at: 2018-03-20 08:15:01 UTC  
> Updated at: 2018-03-20 16:48:29 UTC  
> Merged at: 2018-03-20 16:17:17 UTC  
> Closed at: 2018-03-20 16:17:17 UTC  
> Url: https://github.com/boostorg/signals2/pull/25  

Commit f801fa8f645308296f41c6a59851aacbcaf45fea added explicit  
namespace qualifications. This breaks compilation with  
`BOOST_USE_WINDOWS_H` as the functions are in the global namespace then,  
so revert that part again.

---
