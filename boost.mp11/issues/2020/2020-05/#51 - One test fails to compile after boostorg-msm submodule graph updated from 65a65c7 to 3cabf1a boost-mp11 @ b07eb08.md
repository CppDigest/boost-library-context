# #51 - One test fails to compile after boostorg/msm submodule graph updated from 65a65c7 to 3cabf1a boost/mp11 @ b07eb08 [Closed]

> Username: 215020267  
> Created at: 2020-05-28 08:36:08 UTC  
> Updated at: 2021-03-25 16:32:05 UTC  
> Closed at: 2021-03-25 16:32:05 UTC  
> Url: https://github.com/boostorg/mp11/issues/51  

**Environment:**  
VS 2019 + Windows Server 2016  
  
**Issue description:**  
One test fails to compile after boostorg/msm submodule graph updated from 65a65c7 to 3cabf1a boost/mp11 @ b07eb08. Could you please take a look?  
  
**Reproduce steps:**  
git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
open a VS 2017 x64 command prompt and browse to D:\Boost\src  
.\bootstrap  
.\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
.\b2 variant=release --build-dir=..\out\x64rel address-model=64  
.\b2 -j16 variant=release --build-dir=..\out\amd64rel libs\mp11\test  
[test.log.109.log](https://github.com/boostorg/mp11/files/4694293/test.log.109.log)  
  
  
**ErrorMessage:**  
====== BEGIN OUTPUT ======  
libs\mp11\test\list_version.cpp(15): test 'BOOST_MP11_VERSION == BOOST_VERSION' ('107300' == '107400') failed in function 'int __cdecl main(void)'  
1 error detected.  
   
EXIT STATUS: 1   
====== END OUTPUT ======  
  
      
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\out\amd64rel\boost\bin.v2\libs\mp11\test\list_version.test\msvc-14.2\release\threading-multi\list_version.exe"   > "..\out\amd64rel\boost\bin.v2\libs\mp11\test\list_version.test\msvc-14.2\release\threading-multi\list_version.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "..\out\amd64rel\boost\bin.v2\libs\mp11\test\list_version.test\msvc-14.2\release\threading-multi\list_version.output"  
    echo EXIT STATUS: %status% >> "..\out\amd64rel\boost\bin.v2\libs\mp11\test\list_version.test\msvc-14.2\release\threading-multi\list_version.output"  
    if %status% EQU 0 (  
        copy "..\out\amd64rel\boost\bin.v2\libs\mp11\test\list_version.test\msvc-14.2\release\threading-multi\list_version.output" "..\out\amd64rel\boost\bin.v2\libs\mp11\test\list_version.test\msvc-14.2\release\threading-multi\list_version.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\out\amd64rel\boost\bin.v2\libs\mp11\test\list_version.test\msvc-14.2\release\threading-multi\list_version.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
...failed testing.capture-output ..\out\amd64rel\boost\bin.v2\libs\mp11\test\list_version.test\msvc-14.2\release\threading-multi\list_version.run...

---

## Comment 1

> Username: pdimov  
> Created at: 2020-05-28 12:11:53 UTC  
> Url: https://github.com/boostorg/mp11/issues/51#issuecomment-635300287  

I can't reproduce this failure; it's odd that just `list_version.cpp` fails, because `boost/mp11/list.hpp` gets the `BOOST_MP11_VERSION` macro through its inclusion of `boost/mp11/integral.hpp`, so `integral_version.cpp` must have failed as well.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-05-28 12:15:40 UTC  
> Url: https://github.com/boostorg/mp11/issues/51#issuecomment-635301862  

Looking at the complete log shows all the `_version` tests failing, so this is at least consistent. But `boost/mp11/version.hpp` at revision b07eb08a2f17d0a5ffbb4a268f5c9e8c8fb3e8be is 107400: https://github.com/boostorg/mp11/blob/b07eb08a2f17d0a5ffbb4a268f5c9e8c8fb3e8be/include/boost/mp11/version.hpp
