# #1449 - One test from boost libraries failed to run after boost submodule beast updated from 949fa9 to 1a80dd [Closed]

> Username: spacelg  
> Created at: 2019-02-13 07:32:59 UTC  
> Updated at: 2019-02-25 08:31:05 UTC  
> Closed at: 2019-02-25 08:31:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1449  

Environment:  
 VS 2017 + Windows Server 2016  
  
Issue description:  
We build and run test for boost and we found one test failed to run after boost submodule beast updated from 949fa9 to 1a80dd [boostorg/beast@936e885](https://github.com/boostorg/boost/commit/936e8855c60bb414e2ae486c7470f2c545f53bb0) Could you please take a look?  
  
Reproduce steps:  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\beast\test  
  
ErrorMessage:  
compile-c-c++ ..\out\x64rel\boost\bin.v2\libs\beast\msvc-14.1\release\debug-symbols-on\link-static\threading-multi\test\asio.obj  
asio.cpp  
D:\Boost\src\boost/asio/ssl/detail/openssl_types.hpp(20): fatal error C1083: Cannot open include file: 'openssl/conf.h': No such file or directory  
  
[log_x64_test_124.log](https://github.com/boostorg/beast/files/2859283/log_x64_test_124.log)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-13 08:34:39 UTC  
> Updated at: 2019-02-13 08:35:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1449#issuecomment-463106943  

OpenSSL is now required to build the tests, I'll add a note to the README and build instructions. **vcpkg** is the easiest way to install it. You might need an environment variable (i.e. BOOST_ROOT) for bjam to find it.

---

## Comment 2

> Username: spacelg  
> Created at: 2019-02-20 10:45:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1449#issuecomment-465522738  

Thanks for your reply! Can you provide detailed steps to build the tests with Openssl?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-02-20 14:20:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1449#issuecomment-465594866  

use `vcpkg install openssl` and then re-run your steps. You might need this in your user-config.jam:  
```  
import os ;  
using msvc ;  
local OPENSSL_ROOT = [ os.environ OPENSSL_ROOT ] ;  
project  
  : requirements  
    <include>$(OPENSSL_ROOT)/include  
    <variant>debug:<library-path>$(OPENSSL_ROOT)/debug/lib  
    <variant>release:<library-path>$(OPENSSL_ROOT)/lib  
  ;  
```

---

## Comment 4

> Username: spacelg  
> Created at: 2019-02-25 08:31:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1449#issuecomment-466916412  

Thanks for you information! We've builded the tests pass with Openssl on latest version. So close it.
