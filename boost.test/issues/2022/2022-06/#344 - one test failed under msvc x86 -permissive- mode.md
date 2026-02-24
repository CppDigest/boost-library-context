# #344 - [MSVC] one test failed under msvc x86 /permissive- mode [Open]

> Username: Zhaojun-Liu  
> Created at: 2022-06-23 11:24:46 UTC  
> Updated at: 2022-06-23 11:24:46 UTC  
> Url: https://github.com/boostorg/test/issues/344  

**Issue description:**  
One test fails after runtest in MSVC. Could you please take a look?  
  
**Reproduce steps:**  
1. git clone ​https://github.com/boostorg/boost.git F:\gitP\boostorg\boost  
2. git -C "F:\gitP\boostorg\boost" submodule update --init --recursive  
3. open a VS 2019 x86 command prompt and go to F:\gitP\boostorg\boost  
4. set _CL_=/permissive-  
5. .\bootstrap  
6. .\b2 -j16 variant=release --build-dir=out\x86rel libs\test\test address-model=32 2>&1  
  
**Expected result:**  
test pass.  
  
**Actual result:**  
*** 1 failure is detected in the test module "timeout-error-suite"  
   
EXIT STATUS: 201   
====== END OUTPUT ======  
  
      
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "out\x86rel\boost\bin.v2\libs\test\test\test-timeout-suite.test\msvc-14.2\release\address-model-32\threading-multi\test-timeout-suite.exe" --   > "out\x86rel\boost\bin.v2\libs\test\test\test-timeout-suite.test\msvc-14.2\release\address-model-32\threading-multi\test-timeout-suite.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "out\x86rel\boost\bin.v2\libs\test\test\test-timeout-suite.test\msvc-14.2\release\address-model-32\threading-multi\test-timeout-suite.output"  
    echo EXIT STATUS: %status% >> "out\x86rel\boost\bin.v2\libs\test\test\test-timeout-suite.test\msvc-14.2\release\address-model-32\threading-multi\test-timeout-suite.output"  
    if %status% EQU 0 (  
        copy "out\x86rel\boost\bin.v2\libs\test\test\test-timeout-suite.test\msvc-14.2\release\address-model-32\threading-multi\test-timeout-suite.output" "out\x86rel\boost\bin.v2\libs\test\test\test-timeout-suite.test\msvc-14.2\release\address-model-32\threading-multi\test-timeout-suite.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "out\x86rel\boost\bin.v2\libs\test\test\test-timeout-suite.test\msvc-14.2\release\address-model-32\threading-multi\test-timeout-suite.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
...failed testing.capture-output out\x86rel\boost\bin.v2\libs\test\test\test-timeout-suite.test\msvc-14.2\release\address-model-32\threading-multi\test-timeout-suite.run...  
  
**Detailed log:**  
[test.log.115.out.log](https://github.com/boostorg/test/files/8966758/test.log.115.out.log)
