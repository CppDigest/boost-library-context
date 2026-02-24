# #29 - boostorg/exception failed due to error LNK2019 on MSVC [Closed]

> Username: QuellaZhang  
> Created at: 2019-12-20 09:13:13 UTC  
> Updated at: 2019-12-21 22:20:25 UTC  
> Closed at: 2019-12-21 22:20:25 UTC  
> Url: https://github.com/boostorg/exception/issues/29  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
Boostorg/exception failed to compile when updating Boost version to 68cc708 on MSVC. Could you please take a look?  
  
[log_x64_test_32.log](https://github.com/boostorg/exception/files/3987545/log_x64_test_32.log)  
  
Reproduce steps:  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\exception\test  
  
ErrorMessage:  
no_exceptions_test.obj : error LNK2019: unresolved external symbol "void __cdecl boost::throw_exception(class std::exception const &,struct boost::source_location const &)" (?throw_exception@boost@@YAXAEBVexception@std@@AEBUsource_location@1@@Z) referenced in function main'

---

## Comment 1

> Username: zajo  
> Created at: 2019-12-21 22:20:25 UTC  
> Url: https://github.com/boostorg/exception/issues/29#issuecomment-568216044  

Should be fixed.
