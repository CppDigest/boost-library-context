# #460 - [MSVC][std:c++latest] Boost\libs\config run tests failed due to error C2666: 'boost_no_cxx11_unified_initialization_syntax::IdString::operator ==': overloaded functions have similar conversions [Closed]

> Username: Zhaojun-Liu  
> Created at: 2022-12-07 09:42:30 UTC  
> Updated at: 2022-12-08 11:31:54 UTC  
> Closed at: 2022-12-08 11:31:54 UTC  
> Url: https://github.com/boostorg/config/issues/460  

**Description:**  
After the MSVC team implented [P2468R2 The Equality Operator You Are Looking For](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p2468r2.html), we got a compiler error with /std:c++latest like below, the error is due to the equality operator here https://github.com/boostorg/config/blob/a7276f03975df55243924b4d277f4b1ae32bcfa2/test/boost_no_unified_init.ipp#L37 is missing 'const'. Could you please look this issue? Thanks.  
```  
F:\gitP\boostorg\boost\libs\config\test\boost_no_unified_init.ipp(56): error C2666: 'boost_no_cxx11_unified_initialization_syntax::IdString::operator ==': overloaded functions have similar conversions  
F:\gitP\boostorg\boost\libs\config\test\boost_no_unified_init.ipp(37): note: could be 'bool boost_no_cxx11_unified_initialization_syntax::IdString::operator ==(const boost_no_cxx11_unified_initialization_syntax::IdString &)'  
F:\gitP\boostorg\boost\libs\config\test\boost_no_unified_init.ipp(37): note: or 'bool boost_no_cxx11_unified_initialization_syntax::IdString::operator ==(const boost_no_cxx11_unified_initialization_syntax::IdString &)' [synthesized expression 'y == x']  
F:\gitP\boostorg\boost\libs\config\test\boost_no_unified_init.ipp(56): note: while trying to match the argument list '(boost_no_cxx11_unified_initialization_syntax::IdString, boost_no_cxx11_unified_initialization_syntax::IdString)'  
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
16. .\b2 -j16 variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel libs\config\test address-model=64 > config_test.log  
  
**Detailed log:**  
[config_test.log](https://github.com/boostorg/config/files/10174743/config_test.log)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-12-07 19:07:58 UTC  
> Url: https://github.com/boostorg/config/issues/460#issuecomment-1341454428  

Does the referenced PR fix things?  
  
I've tested with latest Visual Studio17.4.2 (_MSC_FULL_VER=193431935) but was unable to reproduce.

---

## Comment 2

> Username: Zhaojun-Liu  
> Created at: 2022-12-08 01:46:58 UTC  
> Url: https://github.com/boostorg/config/issues/460#issuecomment-1341856354  

> Does the referenced PR fix things?  
>   
> I've tested with latest Visual Studio17.4.2 (_MSC_FULL_VER=193431935) but was unable to reproduce.  
  
Thanks for your reply. I will try to build again according to the PR you mentioned.

---

## Comment 3

> Username: Zhaojun-Liu  
> Created at: 2022-12-08 06:57:46 UTC  
> Url: https://github.com/boostorg/config/issues/460#issuecomment-1342166221  

@jzmaddock I tried the fix and it worked. Thanks. 😃
