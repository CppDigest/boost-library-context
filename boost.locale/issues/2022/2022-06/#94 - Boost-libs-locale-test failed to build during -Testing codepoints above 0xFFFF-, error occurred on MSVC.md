# #94 - Boost\libs\locale\test failed to build during "Testing codepoints above 0xFFFF", error occurred on MSVC [Closed]

> Username: rachelhe123  
> Created at: 2022-06-07 09:05:13 UTC  
> Updated at: 2022-09-17 20:52:09 UTC  
> Closed at: 2022-09-17 20:52:09 UTC  
> Url: https://github.com/boostorg/locale/issues/94  

**Issue Description:**  
Boost\libs\locale\test failed to build during "Testing codepoints above 0xFFFF", error occurred on MSVC . Could you please take a look? The master branch commit we used is a1552ab.  
  
**Build Steps:**  
	1. git clone -c core.autocrlf=true --recursive ​ https://github.com/boostorg/boost.git boost  
	2. open a VS 2019 x86 command prompt and browse to boost  
	3. .\bootstrap  
	4. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=32  
	5. .\b2 variant=release --build-dir=..\out\x86rel address-model=32  
	6. .\b2 -j16 variant=release --build-dir=..\out\x86rel libs\locale\test  
  
**Error Info:**  
testing.capture-output ..\out\x86rel\boost\bin.v2\libs\locale\test\test_std_collate.test\msvc-14.2\release\threading-multi\test_std_collate.run  
        1 file(s) copied.  
testing.capture-output ..\out\x86rel\boost\bin.v2\libs\locale\test\test_codepage.test\msvc-14.2\release\threading-multi\test_codepage.run  
====== BEGIN OUTPUT ======  
- Testing correct invalid bytes skipping  
-- ISO-8859-8  
-- cp932  
Warning: detected unproper support of ja_JP.SJIS locale, disableling it  
Testing for backend std  
Testing wide I/O  
  wchar_t  
    UTF-8  
    Testing codepoints above 0xFFFF  
      Single U+2008A  
Error in line:67 read_file<Char>(f1) == cmp  
Error in line:76 read_file<char>(testo) == file  
      Single U+2008A withing text  
Error in line:67 read_file<Char>(f1) == cmp  
Error in line:76 read_file<char>(testo) == file  
      U+2008A x 1000  
Error in line:67 read_file<Char>(f1) == cmp  
Error in line:76 read_file<char>(testo) == file  
    ISO8859-8  
    ISO8859-1  
Testing charset to/from UTF conversion functions  
  char  
  wchar_t  
Testing utf_to_utf  
  char<-char  
  char<-wchar  
  wchar<-char  
  wchar<-wchar  
Testing for backend winapi  
Testing wide I/O  
  wchar_t  
    UTF-8  
    Testing codepoints above 0xFFFF  
      Single U+2008A  
      Single U+2008A withing text  
      U+2008A x 1000  
Testing charset to/from UTF conversion functions  
  char  
  wchar_t  
Testing utf_to_utf  
  char<-char  
  char<-wchar  
  wchar<-char  
  wchar<-wchar  
  
Passed 366 tests  
Failed 6 tests  
  98.4% of tests completed sucsessefully  
   
EXIT STATUS: 1   
====== END OUTPUT ======  
  
    set Path=F:\gitP\boostorg\out\x86rel\boost\bin.v2\libs\locale\build\msvc-14.2\release\threading-multi;F:\gitP\boostorg\out\x86rel\boost\bin.v2\libs\system\build\msvc-14.2\release\threading-multi;%Path%  
  
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\out\x86rel\boost\bin.v2\libs\locale\test\test_codepage.test\msvc-14.2\release\threading-multi\test_codepage.exe"   > "..\out\x86rel\boost\bin.v2\libs\locale\test\test_codepage.test\msvc-14.2\release\threading-multi\test_codepage.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "..\out\x86rel\boost\bin.v2\libs\locale\test\test_codepage.test\msvc-14.2\release\threading-multi\test_codepage.output"  
    echo EXIT STATUS: %status% >> "..\out\x86rel\boost\bin.v2\libs\locale\test\test_codepage.test\msvc-14.2\release\threading-multi\test_codepage.output"  
    if %status% EQU 0 (  
        copy "..\out\x86rel\boost\bin.v2\libs\locale\test\test_codepage.test\msvc-14.2\release\threading-multi\test_codepage.output" "..\out\x86rel\boost\bin.v2\libs\locale\test\test_codepage.test\msvc-14.2\release\threading-multi\test_codepage.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\out\x86rel\boost\bin.v2\libs\locale\test\test_codepage.test\msvc-14.2\release\threading-multi\test_codepage.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
...failed testing.capture-output ..\out\x86rel\boost\bin.v2\libs\locale\test\test_codepage.test\msvc-14.2\release\threading-multi\test_codepage.run...  
  
See more details in log:   
[test.log.txt](https://github.com/boostorg/locale/files/8851786/test.log.txt)

---

## Comment 1

> Username: Flamefire  
> Created at: 2022-06-07 09:08:41 UTC  
> Url: https://github.com/boostorg/locale/issues/94#issuecomment-1148403961  

Yes this is currently a known issue, the Boost.Locale testsuite is known to fail on various platforms due to changes to the toolchains not accounted for in the tests.  
In particular this is a duplicate of #71. Will be working on this soon, but no ETA yet.

---

## Comment 2

> Username: Flamefire  
> Created at: 2022-09-17 20:52:09 UTC  
> Url: https://github.com/boostorg/locale/issues/94#issuecomment-1250138793  

Fixed by #113
