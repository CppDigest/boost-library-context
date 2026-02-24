# #102 - [MSVC][std:c++latest] Boost\libs\utility run tests failed due to error C2666: 'comparible_UDT::operator ==': overloaded functions have similar conversions [Open]

> Username: Zhaojun-Liu  
> Created at: 2022-12-07 09:00:38 UTC  
> Updated at: 2022-12-07 10:36:04 UTC  
> Url: https://github.com/boostorg/utility/issues/102  

**Description:**  
After the MSVC team implented [P2468R2 The Equality Operator You Are Looking For](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p2468r2.html), we got a compiler error with /std:c++latest like below, the error is due to the equality operator here https://github.com/boostorg/utility/blob/3aa25c9dd789c89f08f92632898693d9997d8a70/test/call_traits_test.cpp#L204 is missing 'const'. Could you please look this issue? Thanks.  
```  
libs\utility\test\call_traits_test.cpp(131): error C2666: 'comparible_UDT::operator ==': overloaded functions have similar conversions  
libs\utility\test\call_traits_test.cpp(204): note: could be 'bool comparible_UDT::operator ==(const comparible_UDT &)'  
libs\utility\test\call_traits_test.cpp(204): note: or 'bool comparible_UDT::operator ==(const comparible_UDT &)' [synthesized expression 'y == x']  
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
9. set _CL_= /std:c++latest  
10. cd F:\gitP\boostorg\boost  
11. .\bootstrap   
12. .\b2 headers variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel address-model=64  
13. .\b2 variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel address-model=64  
14. set _CL_=/D_SILENCE_EXPERIMENTAL_FILESYSTEM_DEPRECATION_WARNING /D_HAS_DEPRECATED_ADAPTOR_TYPEDEFS=1 /D_HAS_AUTO_PTR_ETC=1 /D_HAS_DEPRECATED_RAW_STORAGE_ITERATOR=1 /D_HAS_DEPRECATED_TEMPORARY_BUFFER=1 /D_HAS_DEPRECATED_NEGATORS=1 /Zc:enumTypes %_CL_%  
15. set related reference path (in my environment like below):  
    set OPENSSL_ROOT=F:\tools\OpenSSL\64  
    set path=F:\tools\OpenSSL\64\bin;%path%  
    set path=C:\Python\Python27;%path%  
16. .\b2 -j16 variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel libs\utility\test address-model=64 > utility_test.log  
  
**Detailed log:**  
[utility_test.log](https://github.com/boostorg/utility/files/10174385/utility_test.log)

---

## Comment 1

> Username: Lastique  
> Created at: 2022-12-07 10:36:04 UTC  
> Url: https://github.com/boostorg/utility/issues/102#issuecomment-1340737912  

Seems related to https://github.com/boostorg/utility/issues/65.
