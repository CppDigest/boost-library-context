# #162 - 3 tests fails to compile after boostorg/serialization submodule serialization updated from f01148 to f04a8c [Closed]

> Username: spacelg  
> Created at: 2019-10-17 10:56:29 UTC  
> Updated at: 2019-10-18 12:51:18 UTC  
> Closed at: 2019-10-18 12:51:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/162  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
3 tests fails to compile after boostorg/serialization submodule serialization updated from f01148 to f04a8c [boost/serialization@7a16032](https://github.com/boostorg/boost/commit/7a16032488a97abce1c42063bc9e2ed1bfec4c9f). Could you please take a look?    
  
Reproduce steps:  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\multiprecision\test  
7. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\math\test  
8. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\units\test  
  
[log_x64_test_62.log](https://github.com/boostorg/serialization/files/3738825/log_x64_test_62.log)  
[log_x64_test_68.log](https://github.com/boostorg/serialization/files/3738826/log_x64_test_68.log)  
[log_x64_test_115.log](https://github.com/boostorg/serialization/files/3738827/log_x64_test_115.log)  
  
ErrorMessage:  
.\boost/core/nvp.hpp(43): error C2382: 'boost::serialization::make_nvp': redefinition; different exception specifications

---

## Comment 1

> Username: glenfe  
> Created at: 2019-10-17 14:09:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/162#issuecomment-543192523  

Ah, @jzmaddock needs to merge Multiprecision develop to master.

---

## Comment 2

> Username: glenfe  
> Created at: 2019-10-18 12:51:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/162#issuecomment-543726721  

This issue should be resolved now.
