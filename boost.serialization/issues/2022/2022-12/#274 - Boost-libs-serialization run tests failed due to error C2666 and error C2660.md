# #274 - [MSVC][std:c++latest] Boost\libs\serialization run tests failed due to error C2666 and error C2660 [Closed]

> Username: Zhaojun-Liu  
> Created at: 2022-12-07 09:27:55 UTC  
> Updated at: 2023-08-24 18:46:35 UTC  
> Closed at: 2023-08-24 18:46:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/274  

**Description:**  
After the MSVC team implented [P2468R2 The Equality Operator You Are Looking For](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p2468r2.html), we got a compiler error with /std:c++latest like below, these errors are due to that the operator== here https://github.com/boostorg/serialization/blob/0ca603daf99888bf059c01ae1bab1b27dbc35ebe/test/test_multiple_inheritance.cpp#L34 and here https://github.com/boostorg/serialization/blob/0ca603daf99888bf059c01ae1bab1b27dbc35ebe/test/test_multiple_inheritance.cpp#L52 and here https://github.com/boostorg/serialization/blob/0ca603daf99888bf059c01ae1bab1b27dbc35ebe/test/test_multiple_inheritance.cpp#L76 need to take the first parameter by 'const&'. Could you please look this issue? Thanks.  
```  
test_multiple_inheritance.cpp  
libs\serialization\test\test_multiple_inheritance.cpp(123): error C2666: 'Sub::operator ==': overloaded functions have similar conversions  
libs\serialization\test\test_multiple_inheritance.cpp(76): note: could be 'bool Sub::operator ==(Sub &) const'  
libs\serialization\test\test_multiple_inheritance.cpp(76): note: or 'bool Sub::operator ==(Sub &) const' [synthesized expression 'y == x']  
libs\serialization\test\test_multiple_inheritance.cpp(123): note: while trying to match the argument list '(Sub, Sub)'  
libs\serialization\test\test_multiple_inheritance.cpp(123): error C2660: 'boost::detail::test_impl': function does not take 4 arguments  
.\boost/core/lightweight_test.hpp(100): note: see declaration of 'boost::detail::test_impl'  
libs\serialization\test\test_multiple_inheritance.cpp(123): note: while trying to match the argument list '(const char [13], const char [54], int, const char [36])'  
```  
  
**Note:** this issue will be reproduced on next release version of VS (VS17.6 or later)  
  
**Repro steps:**  
1. open VS2019 x64 tools command  
2. git clone ​https://github.com/boostorg/boost.git F:\gitP\boostorg\boost  
3. git -C "F:\gitP\boostorg\boost" fetch --recurse-submodules=no --force  
4. git -C "F:\gitP\boostorg\boost" reset --hard 0c4090b  
5. git -C "F:\gitP\boostorg\boost" submodule sync  
6. git -C "F:\gitP\boostorg\boost" submodule foreach git reset --hard  
7. git -C "F:\gitP\boostorg\boost" submodule foreach git clean -xdf  
8. git -C "F:\gitP\boostorg\boost" submodule update -f --init --recursive  
9. set CL= /std:c++latest  
10. cd F:\gitP\boostorg\boost  
11. .\bootstrap  
12. .\b2 headers variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel address-model=64  
13. .\b2 variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel address-model=64  
14. set CL=/D_SILENCE_EXPERIMENTAL_FILESYSTEM_DEPRECATION_WARNING /D_HAS_DEPRECATED_ADAPTOR_TYPEDEFS=1 /D_HAS_AUTO_PTR_ETC=1 /D_HAS_DEPRECATED_RAW_STORAGE_ITERATOR=1 /D_HAS_DEPRECATED_TEMPORARY_BUFFER=1 /D_HAS_DEPRECATED_NEGATORS=1 /Zc:enumTypes %CL%  
15. set related reference path (in my environment like below):  
      set OPENSSL_ROOT=F:\tools\OpenSSL\64  
      set path=F:\tools\OpenSSL\64\bin;%path%  
      set path=C:\Python\Python27;%path%  
16. .\b2 -j16 variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel libs\serialization\test address-model=64 > serialization_test.log  
  
**Detailed log:**   
[serialization_test.log](https://github.com/boostorg/serialization/files/10174627/serialization_test.log)

---

## Comment 1

> Username: robertramey  
> Created at: 2023-08-24 18:46:22 UTC  
> Url: https://github.com/boostorg/serialization/issues/274#issuecomment-1692233124  

Fixed and Merged
