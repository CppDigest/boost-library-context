# #35 - Please merge the 'develop' branch change into 'master' for the Boost 1.75 release [Closed]

> Username: eldiener  
> Created at: 2020-10-28 17:28:01 UTC  
> Updated at: 2022-04-15 20:17:06 UTC  
> Closed at: 2022-04-15 20:17:05 UTC  
> Url: https://github.com/boostorg/pool/issues/35  

The fix in the 'develop' branch is for the Embarcadero C++ clang-based compilers.

---

## Comment 1

> Username: jeking3  
> Created at: 2022-04-15 20:17:05 UTC  
> Url: https://github.com/boostorg/pool/issues/35#issuecomment-1100372051  

This was done:  
  
```  
commit 0754642bbae3e8c2a03c1694d7129c94de58def0 (tag: boost-1.76.0.beta1, tag: boost-1.76.0, tag: boost-1.75.0.beta1, tag: boost-1.75.0)  
Author: Edward Diener <eldlistmailingz@tropicsoft.com>  
Date:   Thu Apr 16 11:14:50 2020 -0400  
  
    Change __BORLANDC__ to BOOST_BORLANDC, which is defined in Boost config for the Embarcadero non-clang-based compilers. (#33)  
```
