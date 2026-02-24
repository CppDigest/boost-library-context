# #404 - Boost failed when run spirit test with MSVC on Windows. [Closed]

> Username: QuellaZhang  
> Created at: 2018-10-08 06:38:54 UTC  
> Updated at: 2018-10-09 10:37:28 UTC  
> Closed at: 2018-10-09 10:37:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/404  

Boost failed with run time failure when run spirit test with MSVC on Windows, I use latest source on master branch. Could you please help take a look at this?  
  
**Reproduce steps:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2015 x86 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\Release --address-model=32  
5. .\b2 variant=release --build-dir=..\out\Release --address-model=32  
6. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\spirit\test   
  
**Expected result:**  
All tests passed  
  
**Actual result:**  
libs\spirit\test\x3\binary.cpp(65): test 'test_attr("\x01\x02", word, us) && us == 0x0102' failed in function 'int __cdecl main(void)'  
libs\spirit\test\x3\binary.cpp(66): test 'test_attr("\x01\x02\x03\x04", dword, ui) && ui == 0x01020304' failed in function 'int __cdecl main(void)'  
libs\spirit\test\x3\binary.cpp(69): test 'test_attr("\x01\x02\x03\x04\x05\x06\x07\x08", qword, ul) && ul == 0x0102030405060708LL' failed in function 'int __cdecl main(void)'  
libs\spirit\test\x3\binary.cpp(92): test 'test("\x01\x02", word(0x0102))' failed in function 'int __cdecl main(void)'  
libs\spirit\test\x3\binary.cpp(93): test 'test("\x01\x02\x03\x04", dword(0x01020304))' failed in function 'int __cdecl main(void)'  
libs\spirit\test\x3\binary.cpp(96): test 'test("\x01\x02\x03\x04\x05\x06\x07\x08", qword(0x0102030405060708LL))' failed in function 'int __cdecl main(void)'  
6 errors detected.  
   
EXIT STATUS: 1   
====== END OUTPUT ======  
  
      
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\out\x86rel\boost\bin.v2\libs\spirit\test\x3\x3_binary.test\msvc-14.1\release\threading-multi\x3_binary.exe"   > "..\out\x86rel\boost\bin.v2\libs\spirit\test\x3\x3_binary.test\msvc-14.1\release\threading-multi\x3_binary.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "..\out\x86rel\boost\bin.v2\libs\spirit\test\x3\x3_binary.test\msvc-14.1\release\threading-multi\x3_binary.output"  
    echo EXIT STATUS: %status% >> "..\out\x86rel\boost\bin.v2\libs\spirit\test\x3\x3_binary.test\msvc-14.1\release\threading-multi\x3_binary.output"  
    if %status% EQU 0 (  
        copy "..\out\x86rel\boost\bin.v2\libs\spirit\test\x3\x3_binary.test\msvc-14.1\release\threading-multi\x3_binary.output" "..\out\x86rel\boost\bin.v2\libs\spirit\test\x3\x3_binary.test\msvc-14.1\release\threading-multi\x3_binary.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\out\x86rel\boost\bin.v2\libs\spirit\test\x3\x3_binary.test\msvc-14.1\release\threading-multi\x3_binary.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-10-08 10:51:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/404#issuecomment-427791115  

Thanks for the report. The problem is fixed in develop branch (PR #397).
