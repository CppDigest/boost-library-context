# #28 - One test from boost libraries failed to run after boost submodule throw_exception updated from 50c34d to 514355 [Closed]

> Username: spacelg  
> Created at: 2019-06-18 07:43:13 UTC  
> Updated at: 2019-06-22 09:16:04 UTC  
> Closed at: 2019-06-22 09:16:03 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/28  

Environment:  
VS 2017 + Windows Server 2016  
  
One test from boost libraries failed to run after boost submodule throw_exception updated from 50c34d to 514355 [boost/throw_exception@a40a714](https://github.com/boostorg/boost/commit/a40a714491ba4a30d4ad1986ef4b133e3960014b). Could you please take a look?  
  
Reproduce steps:.  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\lexical_cast\test  
  
[log_x64_test_56.log](https://github.com/boostorg/throw_exception/files/3300338/log_x64_test_56.log)  
  
ErrorMessage:  
testing.capture-output ..\out\x64rel\boost\bin.v2\libs\lexical_cast\test\lexical_cast_no_exceptions_test.test\msvc-14.1\release\link-static\threading-multi\lexical_cast_no_exceptions_test.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
unknown location(0): fatal error: in "lexical_cast. Testing with BOOST_NO_EXCEPTIONS/test_exceptions_off": breakpoint encountered  
  
*** 1 failure is detected in the test module "Master Test Suite"  
   
EXIT STATUS: 201   
====== END OUTPUT ======  
  
      
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\out\x64rel\boost\bin.v2\libs\lexical_cast\test\lexical_cast_no_exceptions_test.test\msvc-14.1\release\link-static\threading-multi\lexical_cast_no_exceptions_test.exe"   > "..\out\x64rel\boost\bin.v2\libs\lexical_cast\test\lexical_cast_no_exceptions_test.test\msvc-14.1\release\link-static\threading-multi\lexical_cast_no_exceptions_test.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "..\out\x64rel\boost\bin.v2\libs\lexical_cast\test\lexical_cast_no_exceptions_test.test\msvc-14.1\release\link-static\threading-multi\lexical_cast_no_exceptions_test.output"  
    echo EXIT STATUS: %status% >> "..\out\x64rel\boost\bin.v2\libs\lexical_cast\test\lexical_cast_no_exceptions_test.test\msvc-14.1\release\link-static\threading-multi\lexical_cast_no_exceptions_test.output"  
    if %status% EQU 0 (  
        copy "..\out\x64rel\boost\bin.v2\libs\lexical_cast\test\lexical_cast_no_exceptions_test.test\msvc-14.1\release\link-static\threading-multi\lexical_cast_no_exceptions_test.output" "..\out\x64rel\boost\bin.v2\libs\lexical_cast\test\lexical_cast_no_exceptions_test.test\msvc-14.1\release\link-static\threading-multi\lexical_cast_no_exceptions_test.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\out\x64rel\boost\bin.v2\libs\lexical_cast\test\lexical_cast_no_exceptions_test.test\msvc-14.1\release\link-static\threading-multi\lexical_cast_no_exceptions_test.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
...failed testing.capture-output

---

## Comment 1

> Username: pdimov  
> Created at: 2019-06-18 09:00:08 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/28#issuecomment-503014688  

Thanks for the report. I'll transfer this issue to `lexical_cast`, because the test needs to be updated.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-06-18 09:03:00 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/28#issuecomment-503015754  

`boost::throw_exception` now enforces "must not return" by being declared with `BOOST_NORETURN`, so the test must be updated to not rely on returning from it. https://github.com/boostorg/throw_exception/commit/1e507924cea8af2c57203549e1e132a6f314c475

---

## Comment 3

> Username: apolukhin  
> Created at: 2019-06-22 09:16:03 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/28#issuecomment-504647939  

Additional fixes were applied in https://github.com/boostorg/lexical_cast/commit/3786d41da098a9bdc27e4fce9ea3f95a6e65e233  
  
Will merge to master soon.
