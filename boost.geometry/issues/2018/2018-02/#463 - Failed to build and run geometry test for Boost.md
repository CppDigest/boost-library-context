# #463 - Failed to build and run geometry test for Boost [Closed]

> Username: shanshan0309  
> Created at: 2018-02-28 03:08:10 UTC  
> Updated at: 2018-02-28 07:44:12 UTC  
> Closed at: 2018-02-28 07:44:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/463  

We tried to build and run geometry test for Boost. It failed to build due to error: in "test_main_caller( argc, argv )":  projection: bonne_spheroid distance found: 17 expected: 35. Could you please help take a look at this? Thanks!  
  
**Reproduce steps:**  
1. git clone -c core.autocrlf=true --recursive https://github.com/boostorg/boost.git D:\Boost\src  
2. Open a VS 2015 x86 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\Release --address-model=32  
5. .\b2 variant=release --build-dir=..\out\Release --address-model=32  
6. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\geometry\test  
Expected result:  
All tests passed  
  
**Actual result:**  
Running 1 test case...  
libs/geometry/test/srs/projections_static.cpp(59): error: in "test_main_caller( argc, argv )":  projection: bonne_spheroid distance found: 17 expected: 35  
  
*** 1 failure is detected in the test module "Test Program"  
   
EXIT STATUS: 201   
====== END OUTPUT ======  
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\out\x86rel\boost\bin.v2\libs\geometry\test\srs\srs_projections_static.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\srs_projections_static.exe"   > "..\out\x86rel\boost\bin.v2\libs\geometry\test\srs\srs_projections_static.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\srs_projections_static.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "..\out\x86rel\boost\bin.v2\libs\geometry\test\srs\srs_projections_static.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\srs_projections_static.output"  
    echo EXIT STATUS: %status% >> "..\out\x86rel\boost\bin.v2\libs\geometry\test\srs\srs_projections_static.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\srs_projections_static.output"  
    if %status% EQU 0 (  
        copy "..\out\x86rel\boost\bin.v2\libs\geometry\test\srs\srs_projections_static.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\srs_projections_static.output" "..\out\x86rel\boost\bin.v2\libs\geometry\test\srs\srs_projections_static.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\srs_projections_static.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\out\x86rel\boost\bin.v2\libs\geometry\test\srs\srs_projections_static.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\srs_projections_static.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
...failed testing.capture-output ..\out\x86rel\boost\bin.v2\libs\geometry\test\srs\srs_projections_static.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\srs_projections_static.run...
