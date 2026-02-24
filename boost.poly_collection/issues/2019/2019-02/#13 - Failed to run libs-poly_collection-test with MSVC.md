# #13 - Failed to run libs\poly_collection\test with MSVC [Closed]

> Username: spacelg  
> Created at: 2019-02-25 09:15:50 UTC  
> Updated at: 2019-03-11 09:28:40 UTC  
> Closed at: 2019-03-11 09:28:40 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/13  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
We build and run test for boost we found one test libs\poly_collection\test failed. It can be reproduced on latest version. Could you please take a look?  
  
Reproduce steps:  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\poly_collection\test  
  
ErrorMessage:  
testing.capture-output ..\out\x64rel\boost\bin.v2\libs\poly_collection\test\test_construction.test\msvc-14.1\release\threading-multi\test_construction.run  
====== BEGIN OUTPUT ======  
   
EXIT STATUS: 3   
====== END OUTPUT ======  
  
      
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\out\x64rel\boost\bin.v2\libs\poly_collection\test\test_construction.test\msvc-14.1\release\threading-multi\test_construction.exe"   > "..\out\x64rel\boost\bin.v2\libs\poly_collection\test\test_construction.test\msvc-14.1\release\threading-multi\test_construction.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "..\out\x64rel\boost\bin.v2\libs\poly_collection\test\test_construction.test\msvc-14.1\release\threading-multi\test_construction.output"  
    echo EXIT STATUS: %status% >> "..\out\x64rel\boost\bin.v2\libs\poly_collection\test\test_construction.test\msvc-14.1\release\threading-multi\test_construction.output"  
    if %status% EQU 0 (  
        copy "..\out\x64rel\boost\bin.v2\libs\poly_collection\test\test_construction.test\msvc-14.1\release\threading-multi\test_construction.output" "..\out\x64rel\boost\bin.v2\libs\poly_collection\test\test_construction.test\msvc-14.1\release\threading-multi\test_construction.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\out\x64rel\boost\bin.v2\libs\poly_collection\test\test_construction.test\msvc-14.1\release\threading-multi\test_construction.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
...failed testing.capture-output   
  
[log_x64_test_76.log](https://github.com/boostorg/poly_collection/files/2899699/log_x64_test_76.log)

---

## Comment 1

> Username: joaquintides  
> Created at: 2019-02-25 09:38:54 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/13#issuecomment-466942643  

Hi,  
  
Unfortunately the attached log does not offer much information, except that no test case is signalled as failing and that the problem seems to be triggered by the OS or the standard library (Windows exit code 3 is `ERROR_PATH_NOT_FOUND`, which makes little sense to me).  
  
FWIW, VS2017 is tested succesfully by [AppVeyor](https://ci.appveyor.com/project/joaquintides/poly-collection) and [regression testers](https://www.boost.org/development/tests/develop/developer/poly_collection.html).  
  
Maybe you can try to debug the executable manually so as to try to catch some exception that might give us a clue?

---

## Comment 2

> Username: joaquintides  
> Created at: 2019-03-11 09:28:40 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/13#issuecomment-471465035  

Closing due to lack of further feedback from submitter.
