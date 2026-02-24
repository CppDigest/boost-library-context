# #57 - One test from boost libraries failed to run after boost submodule core updated from d9b524 to 836ae6 [Closed]

> Username: spacelg  
> Created at: 2019-05-06 09:18:38 UTC  
> Updated at: 2019-05-06 10:26:29 UTC  
> Closed at: 2019-05-06 10:26:29 UTC  
> Url: https://github.com/boostorg/core/issues/57  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
One test from boost libraries failed to run after boost submodule core updated from d9b524 to 836ae6 [boost/core@38e8d3f](https://github.com/boostorg/boost/commit/38e8d3f95c8b705c7a16932d4bf5b9e6694941d6). Could you please take a look?  
  
Reproduce steps:  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\detail\test  
  
[log_x64_test_27.log](https://github.com/boostorg/core/files/3147309/log_x64_test_27.log)  
  
ErrorMessage:  
libs\detail\test\test_utf8_codecvt.cpp(283): error C2065: 'BOOST_TRY': undeclared identifier  
libs\detail\test\test_utf8_codecvt.cpp(284): error C2059: syntax error: ';'  
libs\detail\test\test_utf8_codecvt.cpp(287): error C4430: missing type specifier - int assumed.  
libs\detail\test\test_utf8_codecvt.cpp(296): error C2144: syntax error: 'int' should be preceded by ';'  
libs\detail\test\test_utf8_codecvt.cpp(300): error C2143: syntax error: missing ';' before '}'

---

## Comment 1

> Username: Lastique  
> Created at: 2019-05-06 10:26:29 UTC  
> Url: https://github.com/boostorg/core/issues/57#issuecomment-489574430  

Should be fixed by https://github.com/boostorg/detail/commit/fc781c8a5aca36dc32f4d1d5238b7bd2eaa7dc8f.
