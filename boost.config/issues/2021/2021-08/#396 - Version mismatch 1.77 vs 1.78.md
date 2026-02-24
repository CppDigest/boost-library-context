# #396 - Version mismatch 1.77 vs 1.78 [Closed]

> Username: jbanaszczyk  
> Created at: 2021-08-15 12:01:52 UTC  
> Updated at: 2021-08-15 15:19:21 UTC  
> Closed at: 2021-08-15 15:19:21 UTC  
> Url: https://github.com/boostorg/config/issues/396  

Visual Studio error:   
`LINK : fatal error LNK1104: cannot open file 'libboost_locale-vc142-mt-s-x64-1_77.lib'`  
In fact: there is no `libboost_locale-vc142-mt-s-x64-1_77.lib`, but `libboost_locale-vc142-mt-s-x64-1_78.lib` exists  
  
Probably https://github.com/boostorg/config/blob/develop/include/boost/version.hpp should be upgraded to 1.78

---

## Comment 1

> Username: mclow  
> Created at: 2021-08-15 13:57:10 UTC  
> Url: https://github.com/boostorg/config/issues/396#issuecomment-899054705  

https://github.com/boostorg/config/pull/395
