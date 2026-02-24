# #40 - One test from boost libraries failed to run after boost submodule mp11 updated from b23b97 to f64bc3 [Closed]

> Username: spacelg  
> Created at: 2019-09-02 06:34:56 UTC  
> Updated at: 2019-12-04 23:52:37 UTC  
> Closed at: 2019-12-04 23:52:37 UTC  
> Url: https://github.com/boostorg/mp11/issues/40  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
One test from boost libraries failed to run after boost submodule mp11 updated from b23b97 to f64bc3 [boost/mp11@dc2e9c5](https://github.com/boostorg/boost/commit/dc2e9c50d98d5d57a4d4b5dc967561f1866911ee) . Could you please take a look?  
  
Reproduce steps:  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\mp11\test  
  
[log_x64_test_129.log](https://github.com/boostorg/mp11/files/3564736/log_x64_test_129.log)  
  
ErrorMessage:  
testing.capture-output ..\out\x64rel\boost\bin.v2\libs\mp11\test\version.test\msvc-14.1\release\threading-multi\version.run  
====== BEGIN OUTPUT ======  
libs\mp11\test\version.cpp(15): test 'BOOST_MP11_VERSION == BOOST_VERSION' ('107200' == '107100') failed in function 'int __cdecl main(void)'  
1 error detected.  
   
EXIT STATUS: 1   
====== END OUTPUT ======  
  
      
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\out\x64rel\boost\bin.v2\libs\mp11\test\version.test\msvc-14.1\release\threading-multi\version.exe"   > "..\out\x64rel\boost\bin.v2\libs\mp11\test\version.test\msvc-14.1\release\threading-multi\version.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "..\out\x64rel\boost\bin.v2\libs\mp11\test\version.test\msvc-14.1\release\threading-multi\version.output"  
    echo EXIT STATUS: %status% >> "..\out\x64rel\boost\bin.v2\libs\mp11\test\version.test\msvc-14.1\release\threading-multi\version.output"  
    if %status% EQU 0 (  
        copy "..\out\x64rel\boost\bin.v2\libs\mp11\test\version.test\msvc-14.1\release\threading-multi\version.output" "..\out\x64rel\boost\bin.v2\libs\mp11\test\version.test\msvc-14.1\release\threading-multi\version.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\out\x64rel\boost\bin.v2\libs\mp11\test\version.test\msvc-14.1\release\threading-multi\version.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
...failed testing.capture-output ..\out\x64rel\boost\bin.v2\libs\mp11\test\version.test\msvc-14.1\release\threading-multi\version.run...  
testing.capture-output ..\out\x64rel\boost\bin.v2\libs\mp11\test\mpl_tuple.test\msvc-14.1\release\threading-multi\mpl_tuple.run  
        1 file(s) copied.  
**passed** ..\out\x64rel\boost\bin.v2\libs\mp11\test\mpl_tuple.test\msvc-14.1\release\threading-multi\mpl_tuple.test  
compile-c-c++ ..\out\x64rel\boost\bin.v2\libs\mp11\test\mp11_version.test\msvc-14.1\release\threading-multi\mp11_version.obj  
mp11_version.cpp  
msvc.link ..\out\x64rel\boost\bin.v2\libs\mp11\test\mp11_version.test\msvc-14.1\release\threading-multi\mp11_version.exe  
msvc.manifest ..\out\x64rel\boost\bin.v2\libs\mp11\test\mp11_version.test\msvc-14.1\release\threading-multi\mp11_version.exe  
testing.capture-output ..\out\x64rel\boost\bin.v2\libs\mp11\test\mp11_version.test\msvc-14.1\release\threading-multi\mp11_version.run  
====== BEGIN OUTPUT ======  
libs\mp11\test\mp11_version.cpp(15): test 'BOOST_MP11_VERSION == BOOST_VERSION' ('107200' == '107100') failed in function 'int __cdecl main(void)'  
1 error detected.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-12-04 23:52:37 UTC  
> Url: https://github.com/boostorg/mp11/issues/40#issuecomment-561898396  

This shouldn't be an issue any longer, although a similar one will be popping up after each release, owing to the versions getting out of sync for a brief period before being updated everywhere.
