# #245 - Boost failed when run config test with MSVC on Windows. [Closed]

> Username: QuellaZhang  
> Created at: 2018-10-08 06:44:06 UTC  
> Updated at: 2018-10-08 17:01:12 UTC  
> Closed at: 2018-10-08 17:01:12 UTC  
> Url: https://github.com/boostorg/config/issues/245  

Boost failed with run time failure when run config test with MSVC on Windows, I use latest source on master branch. Could you please help take a look at this?  
  
**Reproduce steps:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2015 x86 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\Release --address-model=32  
5. .\b2 variant=release --build-dir=..\out\Release --address-model=32  
6. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\config\test  
  
   
  
**Expected result:**  
All tests passed  
  
**Actual result:**  
Testing bool (size 1) min: 0, max: 1  
Testing char (size 1) min: -128, max: 127  
Testing signed char (size 1) min: -128, max: 127  
Testing unsigned char (size 1) min: 0, max: 255  
Testing wchar_t (size 2) min: 0, max: 65535  
Testing short (size 2) min: -32768, max: 32767  
Testing unsigned short (size 2) min: 0, max: 65535  
Testing int (size 4) min: -2147483648, max: 2147483647  
Testing unsigned int (size 4) min: 0, max: 4294967295  
Testing long (size 4) min: -2147483648, max: 2147483647  
Testing unsigned long (size 4) min: 0, max: 4294967295  
Testing long long (size 8) min: -9223372036854775808, max: 9223372036854775807  
Testing unsigned long long (size 8) min: 0, max: 18446744073709551615  
Testing __int64 (size 8) min: -9223372036854775808, max: 9223372036854775807  
Testing unsigned __int64 (size 8) min: 0, max: 18446744073709551615  
  
Testing float  
IEEE-compatible: 1, traps: 0, bounded: 1, exact: 0  
min: 1.17549e-38, max: 3.40282e+38  
infinity: inf, QNaN: nan  
hex value of max is: ffff7f7f  
hex value of infinity is: 0000807f  
hex value of qnan is: 0000c07f  
hex value of snan is: 0100c07f  
  
Testing double  
IEEE-compatible: 1, traps: 0, bounded: 1, exact: 0  
min: 2.22507e-308, max: 1.79769e+308  
infinity: inf, QNaN: nan  
hex value of max is: ffffffffffffef7f  
hex value of infinity is: 000000000000f07f  
hex value of qnan is: 000000000000f87f  
hex value of snan is: 010000000000f87f  
  
Testing long double  
IEEE-compatible: 1, traps: 0, bounded: 1, exact: 0  
min: 2.22507e-308, max: 1.79769e+308  
infinity: inf, QNaN: nan  
hex value of max is: ffffffffffffef7f  
hex value of infinity is: 000000000000f07f  
hex value of qnan is: 000000000000f87f  
hex value of snan is: 010000000000f87f  
main() should return report_errors()  
   
EXIT STATUS: 3   
====== END OUTPUT ======  
  
      
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\out\x86rel\boost\bin.v2\libs\config\test\limits_test.test\msvc-14.1\release\threading-multi\limits_test.exe"   > "..\out\x86rel\boost\bin.v2\libs\config\test\limits_test.test\msvc-14.1\release\threading-multi\limits_test.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "..\out\x86rel\boost\bin.v2\libs\config\test\limits_test.test\msvc-14.1\release\threading-multi\limits_test.output"  
    echo EXIT STATUS: %status% >> "..\out\x86rel\boost\bin.v2\libs\config\test\limits_test.test\msvc-14.1\release\threading-multi\limits_test.output"  
    if %status% EQU 0 (  
        copy "..\out\x86rel\boost\bin.v2\libs\config\test\limits_test.test\msvc-14.1\release\threading-multi\limits_test.output" "..\out\x86rel\boost\bin.v2\libs\config\test\limits_test.test\msvc-14.1\release\threading-multi\limits_test.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\out\x86rel\boost\bin.v2\libs\config\test\limits_test.test\msvc-14.1\release\threading-multi\limits_test.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-10-08 17:01:12 UTC  
> Url: https://github.com/boostorg/config/issues/245#issuecomment-427908257  

This was fixed in https://github.com/boostorg/config/commit/cec1a860e948e361a13a0d8fa2ac1b217ba6478f.  Not quite in master yet...
