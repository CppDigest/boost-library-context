# #28 - Please merge the 'develop' branch change into 'master' for the Boost 1.75 release [Closed]

> Username: eldiener  
> Created at: 2020-10-28 17:30:03 UTC  
> Updated at: 2022-04-24 13:30:32 UTC  
> Closed at: 2022-04-24 13:30:31 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/28  

The fix in the 'develop' branch is for the Embarcadero C++ clang-based compilers.

---

## Comment 1

> Username: jeking3  
> Created at: 2022-04-24 13:30:31 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/28#issuecomment-1107842604  

```  
commit 0d547c6e6e4819ab47ecd2b0f78c15d55816fa23 (tag: boost-1.76.0.beta1, tag: boost-1.76.0, tag: boost-1.75.0.beta1, tag: boost-1.75.0)  
Author: Edward Diener <eldlistmailingz@tropicsoft.com>  
Date:   Thu Apr 16 10:49:47 2020 -0400  
  
    Change __BORLANDC__ to BOOST_BORLANDC, which is defined in Boost config for the Embarcadero non-clang-based compilers. (#26)  
```
