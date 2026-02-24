# #35 - Please merge the 'develop' branch change into 'master' for the Boost 1.75 release [Closed]

> Username: eldiener  
> Created at: 2020-10-28 15:40:51 UTC  
> Updated at: 2022-02-13 19:39:08 UTC  
> Closed at: 2022-02-13 19:39:08 UTC  
> Url: https://github.com/boostorg/assign/issues/35  

The fix in the 'develop' branch is for the Embarcadero C++ clang-based compilers.

---

## Comment 1

> Username: jeking3  
> Created at: 2022-02-13 19:39:08 UTC  
> Url: https://github.com/boostorg/assign/issues/35#issuecomment-1038378185  

Looks like this got into release 1.75.0; closing  
  
```  
commit 722683bda9d8bd66231dc2418ba3b9c2879443c2 (tag: boost-1.76.0.beta1, tag: boost-1.76.0, tag: boost-1.75.0.beta1, tag: boost-1.75.0)  
Author: Edward Diener <eldlistmailingz@tropicsoft.com>  
Date:   Thu Apr 16 10:50:32 2020 -0400  
  
    Change __BORLANDC__ to BOOST_BORLANDC, which is defined in Boost config for the Embarcadero non-clang-based compilers. (#34)  
```
