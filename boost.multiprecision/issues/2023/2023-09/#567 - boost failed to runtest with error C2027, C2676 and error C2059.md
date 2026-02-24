# #567 - boost failed to runtest with error C2027, C2676 and error C2059 [Closed]

> Username: Zhaojun-Liu  
> Created at: 2023-09-21 02:35:16 UTC  
> Updated at: 2023-11-03 06:42:07 UTC  
> Closed at: 2023-11-03 06:42:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/567  

Recently we updated the boost commit, the below tests failed with `error C2027: use of undefined type 'boost::array<boost::array<T,3>,351>'` and `error C2676: binary '[': 'const boost::array<boost::array<T,3>,351>' does not define this operator or a conversion to a type acceptable to the predefined operator` and `error C2059: syntax error: ')'`  
```  
...failed compile-c-c++ F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\multiprecision\test\test_cos_cpp_dec_float.test\msvc-14.3\release\link-static\runtime-link-static\threading-multi\test_cos.obj...  
...failed compile-c-c++ F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\multiprecision\test\test_sin_cpp_dec_float.test\msvc-14.3\release\link-static\runtime-link-static\threading-multi\test_sin.obj...  
...failed compile-c-c++ F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\multiprecision\test\number_concept_check_cpp_dec_float.test\msvc-14.3\release\optimization-space\runtime-link-static\threading-multi\number_concept_check_cpp_dec_float.obj...  
...failed compile-c-c++ F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\multiprecision\test\number_concept_check_cpp_bin_float.test\msvc-14.3\release\optimization-space\runtime-link-static\threading-multi\number_concept_check_cpp_bin_float.obj...  
...failed compile-c-c++ F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\multiprecision\test\number_concept_check_backend_concept.test\msvc-14.3\release\optimization-space\runtime-link-static\threading-multi\number_concept_check_backend_concept.obj...  
...failed updating 7 targets...  
```  
**Repro steps:** (Open vs2022 x64 native Tools Command Prompt)  
1. git clone https://github.com/boostorg/boost F:\gitP\boostorg\boost  
2. git -C "F:\gitP\boostorg\boost" fetch --recurse-submodules=no --force  
3. git -C "F:\gitP\boostorg\boost" reset --hard 10994fe  
4. git -C "F:\gitP\boostorg\boost" submodule sync  
5. git -C "F:\gitP\boostorg\boost" submodule foreach git reset --hard  
6. git -C "F:\gitP\boostorg\boost" submodule foreach git clean -xdf  
7. git -C "F:\gitP\boostorg\boost" submodule update -f --init --recursive  
8. cd F:\gitP\boostorg\boost  
9. .\bootstrap 2>&1  
10. .\b2 headers variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel address-model=64 2>&1  
11. .\b2 variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel address-model=64 2>&1  
12. set _CL_=/D_DISABLE_CONSTEXPR_MUTEX_CONSTRUCTOR /D_SILENCE_EXPERIMENTAL_FILESYSTEM_DEPRECATION_WARNING /DBOOST_TIMER_ENABLE_DEPRECATED  
13. set OPENSSL_ROOT=F:\tools\OpenSSL\64  
14. set path=F:\tools\OpenSSL\64\bin;%path%  
15. set path=C:\Python\Python27;%path%  
16. .\b2 -j16 variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel libs\multiprecision\test address-model=64 2>&1  
  
**Detailed log:**  
[test.117.log](https://github.com/boostorg/multiprecision/files/12679470/test.117.log)  
  
**More info:**  
the boost commit 2fe33ef is ok.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2023-09-21 16:48:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/567#issuecomment-1729949221  

What stikes me as odd, ... how did `boost::array<boost::array<T,3>,351>` get in there (or somewhere) after standalone?  
  
Let me look if it's in the code base or a third party-lib mixing it up, ...?  
  
Cc: @jzmaddock and @mborland

---

## Comment 2

> Username: ckormanyos  
> Created at: 2023-09-21 16:48:44 UTC  
> Updated at: 2023-09-21 16:51:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/567#issuecomment-1729950059  

I wonder if the client code used `boost::array` parasitically and it was previously available?

---

## Comment 3

> Username: ckormanyos  
> Created at: 2023-09-21 16:51:08 UTC  
> Updated at: 2023-09-21 16:51:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/567#issuecomment-1729953538  

I searched, ... and I can't find any use of `boost::array` in the `develop` branch of Multiprecison.  
  
Maybe it could be another header included in the include paths or possibly (less likely) something in the client code.

---

## Comment 4

> Username: mborland  
> Created at: 2023-09-22 06:49:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/567#issuecomment-1730893411  

I only pulled all of the boost dependencies out of the include folder. I'll just do a complete replacement of `boost::array` with `std::array` in the tests.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2023-09-22 11:58:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/567#issuecomment-1731295809  

> do a complete replacement of `boost::array` with `std::array` in the tests.  
  
Oh great catch Matt (@mborland). Many thanks.
