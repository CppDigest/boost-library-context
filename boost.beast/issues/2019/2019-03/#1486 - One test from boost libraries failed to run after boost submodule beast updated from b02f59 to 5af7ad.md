# #1486 - One test from boost libraries failed to run after boost submodule beast updated from b02f59 to 5af7ad [Closed]

> Username: spacelg  
> Created at: 2019-03-01 10:07:10 UTC  
> Updated at: 2019-03-03 17:35:26 UTC  
> Closed at: 2019-03-03 17:35:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1486  

Environment:  
 VS 2017 + Windows Server 2016  
  
Issue description:  
One test from boost libraries failed to run after boost submodule beast updated from b02f59 to 5af7ad [boost/beast@1dbdcd6](https://github.com/boostorg/boost/commit/1dbdcd6693da58d8b8dce6c7fa9d0850b7424055). Could you please take a look?  
  
Reproduce steps:.  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\beast\test  
  
ErrorMessage:  
D:\Boost\src\boost/asio/windows/basic_random_access_handle.hpp(92): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
D:\Boost\src\boost/asio/windows/basic_random_access_handle.hpp(93): error C2143: syntax error: missing ',' before '&'  
D:\Boost\src\boost/asio/windows/basic_random_access_handle.hpp(113): error C2988: unrecognizable template declaration/definition  
D:\Boost\src\boost/asio/windows/basic_random_access_handle.hpp(94): error C3861: 'handle': identifier not found  
  
[log_x64_test_124.log](https://github.com/boostorg/beast/files/2918956/log_x64_test_124.log)

---

## Comment 1

> Username: djarek  
> Created at: 2019-03-01 12:33:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1486#issuecomment-468650620  

Try checking out `libs/asio` submodule to the develop branch. There are some recent changes in ASIO that are not yet merged to master but are required to successfully build on Windows.

---

## Comment 2

> Username: djarek  
> Created at: 2019-03-03 17:33:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1486#issuecomment-469045165  

The fixes were merged to ASIO's master branch.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-03-03 17:35:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1486#issuecomment-469045344  

This is resolved
