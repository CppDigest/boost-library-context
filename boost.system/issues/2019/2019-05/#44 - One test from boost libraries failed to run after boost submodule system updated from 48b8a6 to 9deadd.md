# #44 - One test from boost libraries failed to run after boost submodule system updated from 48b8a6 to 9deadd [Closed]

> Username: spacelg  
> Created at: 2019-05-08 05:51:10 UTC  
> Updated at: 2019-05-08 13:34:06 UTC  
> Closed at: 2019-05-08 13:34:05 UTC  
> Url: https://github.com/boostorg/system/issues/44  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
One test from boost libraries failed to run after boost submodule system updated from 48b8a6 to 9deadd [boost/system@102ba2a](https://github.com/boostorg/boost/commit/102ba2adaf15f78b11a9b98d50f0e1bc138af947).Could you please take a look?  
  
Reproduce steps:.  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\chrono\test  
  
[log_x86_test_12.log](https://github.com/boostorg/system/files/3155708/log_x86_test_12.log)  
  
ErrorMessage:  
libs\chrono\test\..\example\xtime.cpp(51): error C2011: 'xtime': 'struct' type redefinition  
libs\chrono\test\..\example\xtime.cpp(79): error C2027: use of undefined type 'xtime'  
libs\chrono\test\..\example\xtime.cpp(90): error C2079: 'xt' uses undefined struct 'xtime'  
libs\chrono\test\..\example\xtime.cpp(91): error C2664: 'void print(xtime)': cannot convert argument 1 from 'int' to 'xtime'

---

## Comment 1

> Username: pdimov  
> Created at: 2019-05-08 13:34:05 UTC  
> Url: https://github.com/boostorg/system/issues/44#issuecomment-490487457  

Thanks for the report, I've opened https://github.com/boostorg/chrono/pull/39 with a fix.
