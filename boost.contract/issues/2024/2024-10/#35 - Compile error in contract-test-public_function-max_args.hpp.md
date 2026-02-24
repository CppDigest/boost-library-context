# #35 - [MSVC] Compile error in contract\test\public_function\max_args.hpp [Closed]

> Username: NEIL-smtg  
> Created at: 2024-10-03 03:50:28 UTC  
> Updated at: 2025-06-01 23:27:20 UTC  
> Closed at: 2025-06-01 23:27:20 UTC  
> Url: https://github.com/boostorg/contract/issues/35  

Hi, I work on MSVC compiler testing, and we regularly build popular open-source project including Boost, with development builds of MSVC in order to find and fix regressions before they ship and cause problems for you.  
  
Recently, as I build Boost from source with MSVC, I encountered some errors as following:  
```  
C:\gitP\boostorg\boost\libs\contract\test\public_function\max_args.hpp(200): error C2065: 'BOOST_CONTRACT_TEST_MAX_ARGS_N_': undeclared identifier  
C:\gitP\boostorg\boost\libs\contract\test\public_function\max_args.hpp(199): error C2059: syntax error: ')'  
warning C1511: creating 'd:\output\Boost\preprocessed_repro_test\max_args0.rsp' for capture_repro  
  
    call "C:\gitP\boostorg\boost\amd64\boost\bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo "libs\contract\test\public_function\max_args0.cpp" -c -Fo"C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\contract\test\public_function-max_args0.test\msvc-14.3\release\x86_64\threading-multi\public_function\max_args0.obj"     -TP /wd4675 /EHs /GR /Zc:throwingNew /O2 /Ob2 /W3 /MD /Zc:forScope /Zc:wchar_t /Zc:inline /Gw /favor:blend -DBOOST_ALL_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_CONTRACT_DYN_LINK -DBOOST_CONTRACT_MAX_ARGS=0 -DNDEBUG "-I." "-Ilibs\contract\test\public_function"   
  
...failed compile-c-c++ C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\contract\test\public_function-max_args0.test\msvc-14.3\release\x86_64\threading-multi\public_function\max_args0.obj...  
```  
  
Note: This is only part of the errors, refer test.log.txt for more detail.   
  
Build log: [build_log.txt](https://github.com/user-attachments/files/17238802/build_log.txt)  
Test log: [Test.log.txt](https://github.com/user-attachments/files/17238803/Test.log.txt)  
  
Steps to reproduce:  
  
1. git clone https://github.com/boostorg/boost.git  
2. cd boostorg\boost  
3. git fetch --recurse-submodules=no --force  
4. git -C "C:\gitP\boostorg\boost" submodule sync  
5. git submodule foreach git reset --hard  
6. git submodule foreach git clean -xdf  
7. git submodule update --init --recursive  
8. set \_CL\_=/DBOOST_TIMER_ENABLE_DEPRECATED  
9. .\bootstrap 2>&1  
10. .\b2 headers variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86 2>&1  
11. .\b2 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86 2>&1  
12. .\b2 -j16 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 libs\contract\test address-model=64 2>&1

---

## Comment 1

> Username: jeking3  
> Created at: 2025-06-01 16:06:42 UTC  
> Updated at: 2025-06-01 16:07:53 UTC  
> Url: https://github.com/boostorg/contract/issues/35#issuecomment-2927466708  

The header `<boost/preprocessor/repetition/enum.hpp>` is missing, causing this.  Will be fixed in the next clump that gets CI running again properly.  It was likely pruned in the past, so it used to work, and now it doesn't expand properly.  If you preprocess it you see the BOOST_PP_ENUM macro in the output.
