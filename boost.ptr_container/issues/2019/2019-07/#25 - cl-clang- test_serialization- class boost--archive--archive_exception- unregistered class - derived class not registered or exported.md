# #25 - cl-clang: test_serialization: class boost::archive::archive_exception: unregistered class - derived class not registered or exported [Open]

> Username: jeking3  
> Created at: 2019-07-05 21:24:29 UTC  
> Updated at: 2019-07-05 21:24:29 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/25  

New issue uncovered by adding a cl-clang Appveyor build job:  
  
https://ci.appveyor.com/project/jeking3/ptr-container-lviqw/builds/25773256/job/uvt8w5fjj452kjw4#L1554  
```  
testing.capture-output bin.v2\libs\ptr_container\test\serialization.test\clang-win-8.0.0\release\address-model-64\cxxstd-11-iso\threading-multi\serialization.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
unknown location(0): fatal error: in "Pointer Container Test Suite/test_serialization": class boost::archive::archive_exception: unregistered class - derived class not registered or exported  
*** 1 failure is detected in the test module "Master Test Suite"  
   
EXIT STATUS: 201   
====== END OUTPUT ======  
    set Path=C:\projects\boost-root\bin.v2\libs\serialization\build\clang-win-8.0.0\release\address-model-64\cxxstd-11-iso\threading-multi;%Path%  
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "bin.v2\libs\ptr_container\test\serialization.test\clang-win-8.0.0\release\address-model-64\cxxstd-11-iso\threading-multi\serialization.exe"   > "bin.v2\libs\ptr_container\test\serialization.test\clang-win-8.0.0\release\address-model-64\cxxstd-11-iso\threading-multi\serialization.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "bin.v2\libs\ptr_container\test\serialization.test\clang-win-8.0.0\release\address-model-64\cxxstd-11-iso\threading-multi\serialization.output"  
    echo EXIT STATUS: %status% >> "bin.v2\libs\ptr_container\test\serialization.test\clang-win-8.0.0\release\address-model-64\cxxstd-11-iso\threading-multi\serialization.output"  
    if %status% EQU 0 (  
        copy "bin.v2\libs\ptr_container\test\serialization.test\clang-win-8.0.0\release\address-model-64\cxxstd-11-iso\threading-multi\serialization.output" "bin.v2\libs\ptr_container\test\serialization.test\clang-win-8.0.0\release\address-model-64\cxxstd-11-iso\threading-multi\serialization.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "bin.v2\libs\ptr_container\test\serialization.test\clang-win-8.0.0\release\address-model-64\cxxstd-11-iso\threading-multi\serialization.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
...failed testing.capture-output bin.v2\libs\ptr_container\test\serialization.test\clang-win-8.0.0\release\address-model-64\cxxstd-11-iso\threading-multi\serialization.run...  
```
