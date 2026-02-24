# #47 Add BOOST_SKIP_INSTALL_RULES check [Merged]

> Username: BraHei  
> Created at: 2023-11-24 12:42:41 UTC  
> Updated at: 2023-12-21 23:41:51 UTC  
> Merged at: 2023-12-21 23:41:51 UTC  
> Closed at: 2023-12-21 23:41:51 UTC  
> Url: https://github.com/boostorg/cmake/pull/47  

Added a check for BOOST_SKIP_INSTALL_RULES. If it set outside of the project (through the CACHE) it will not override it.  
  
See issue #46

---
