# #31 - Two tests from boost libraries failed to run after boost submodule signals2 updated from 0c51b5 to 19bf28 [Closed]

> Username: spacelg  
> Created at: 2018-12-26 07:44:33 UTC  
> Updated at: 2018-12-28 20:15:48 UTC  
> Closed at: 2018-12-28 20:15:47 UTC  
> Url: https://github.com/boostorg/signals2/issues/31  

Environment:  
 VS 2017 + Windows Server 2016  
  
Issue description:  
 We build and run test for boost and we found two tests failed to run after boost submodule signals2 updated from 0c51b5 (master) to 19bf28 [boostorg/signals2@358f052](https://github.com/boostorg/boost/commit/358f05257e03ff47c726a0596ef65ffb9fddd124) Could you please take a look?  
  
Reproduce steps:  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x86 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\Release --address-model=32  
5. .\b2 variant=release --build-dir=..\out\Release --address-model=32  
6. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\signals2\test  
7. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\phoenix\test  
  
ErrorMessage:  
.\boost/signals2/signal_type.hpp(26): fatal error C1083: Cannot open include file: 'boost/parameter/template_keyword.hpp': No such file or directory   
  
[log_x86_test_96.log](https://github.com/boostorg/signals2/files/2709737/log_x86_test_96.log)  
[log_x86_test_74.log](https://github.com/boostorg/signals2/files/2709739/log_x86_test_74.log)

---

## Comment 1

> Username: fmhess  
> Created at: 2018-12-26 15:39:56 UTC  
> Url: https://github.com/boostorg/signals2/issues/31#issuecomment-449983195  

Thanks for pointing this out.  Signals2 master has gotten a bit ahead of  
boost.parameter master.  I may need to temporarily revert some stuff.

---

## Comment 2

> Username: fmhess  
> Created at: 2018-12-28 20:15:47 UTC  
> Url: https://github.com/boostorg/signals2/issues/31#issuecomment-450420501  

I've reverted the problem commit until the changes in Boost.Parameter it requires get merged to master.
