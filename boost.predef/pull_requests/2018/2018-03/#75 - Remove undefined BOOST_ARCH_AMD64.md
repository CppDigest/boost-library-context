# #75 Remove undefined BOOST_ARCH_AMD64 [Merged]

> Username: pkolbus  
> Created at: 2018-03-27 03:51:24 UTC  
> Updated at: 2018-05-13 15:19:21 UTC  
> Merged at: 2018-05-13 15:19:21 UTC  
> Closed at: 2018-05-13 15:19:21 UTC  
> Url: https://github.com/boostorg/predef/pull/75  

This PR fixes an instance of MSVC warning C4668 (undefined preprocessor macro) by removing a reference to BOOST_ARCH_AMD64. This symbol has not been defined since commit b10e349.

---
