# #451 - Test matrix failures [Closed]

> Username: vinniefalco  
> Created at: 2020-10-21 05:02:56 UTC  
> Updated at: 2020-10-21 17:50:40 UTC  
> Closed at: 2020-10-21 17:50:40 UTC  
> Url: https://github.com/boostorg/json/issues/451  

See:  
https://www.boost.org/development/tests/develop/developer/json.html

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-10-21 15:00:46 UTC  
> Url: https://github.com/boostorg/json/issues/451#issuecomment-713640815  

@vinniefalco failures with associated reasons:  
```  
teeks99-dkr-dc10-98: c++98  
teeks99-dkr-dc10-g98: c++98  
teeks99-dkr-dc11-98: c++98  
teeks99-dkr-dc11-g98: c++98  
teeks99-dkr-dc12-98: c++98  
teeks99-dkr-dc12-g98: c++98  
eeks99-dkr-dc3.4-98: c++98  
teeks99-dkr-dc3.4-g98: c++98  
teeks99-dkr-dg4.6-0x: c++0x, incomplete c++11 support  
teeks99-dkr-dg4.6-98: c++98  
teeks99-dkr-dg4.6-g0x: c++0x, incomplete c++11 support  
teeks99-dkr-dg4.6-g98: c++98  
teeks99-dkr-dg4.6-opt: c++0x, incomplete c++11 support  
teeks99-dkr-dg4.6-warn: c++0x, incomplete c++11 support  
teeks99-dkr-dg4.7: c++11, fixable, https://wg21.cmeerw.net/cwg/issue1104 not implemented, use alignas(T) instead of alignas(alignof(T))  
teeks99-dkr-dg9-98: c++98  
teeks99-dkr-dg9-g98: c++98  
teeks99-09-v10d-64onAMD64: incomplete c++11 support  
teeks99-09-v11d-64onAMD64: incomplete c++11 support  
teeks99-09-v12d-64onAMD64: c++11, potentially fixable, use noexcept(true) instead of noexcept  
teeks99-09-v14d-64onAMD64: c++11, potentially fixable  
teeks99-dkr-dc6-17: c++17, use std::is_same instead of std::is_same_v  
```
