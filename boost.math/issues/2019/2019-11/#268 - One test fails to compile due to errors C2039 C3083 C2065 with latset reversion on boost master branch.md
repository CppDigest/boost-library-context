# #268 - One test fails to compile due to errors C2039 C3083 C2065 with latset reversion on boost master branch [Closed]

> Username: spacelg  
> Created at: 2019-11-04 09:20:28 UTC  
> Updated at: 2019-11-08 14:03:19 UTC  
> Closed at: 2019-11-08 14:03:19 UTC  
> Url: https://github.com/boostorg/math/issues/268  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
One test fails to compile due to errors C2039 C3083 C2065 with latset reversion on boost master branch. We found that these errors are generated whenever the "libs\math\test\test_lambert_w.cpp" file is compiled. In addition to these errors when executing the command as a whole, a bullet crash occurs when running nonfinite_serialization_archives.exe. Could you please take a look?  
  
Reproduce steps:  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\math\test  
  
[log_x64_test.log](https://github.com/boostorg/math/files/3803256/log_x64_test.log)  
  
ErrorMessage:  
libs\math\test\test_lambert_w.cpp(804): error C2039: 'unit_test_framework': is not a member of 'boost'  
D:\Boost\src\libs\math\test\table_type.hpp(17): note: see declaration of 'boost'  
libs\math\test\test_lambert_w.cpp(804): error C3083: 'unit_test_framework': the symbol to the left of a '::' must be a type  
libs\math\test\test_lambert_w.cpp(804): error C2039: 'unit_test_log': is not a member of 'boost'  
D:\Boost\src\libs\math\test\table_type.hpp(17): note: see declaration of 'boost'  
libs\math\test\test_lambert_w.cpp(804): error C2065: 'unit_test_log': undeclared identifier  
libs\math\test\test_lambert_w.cpp(804): error C3083: 'unit_test_framework': the symbol to the left of a '::' must be a type  
libs\math\test\test_lambert_w.cpp(804): error C2039: 'log_messages': is not a member of 'boost'  
D:\Boost\src\libs\math\test\table_type.hpp(17): note: see declaration of 'boost'  
libs\math\test\test_lambert_w.cpp(804): error C2065: 'log_messages': undeclared identifier  
  
..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_serialization_archives.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_serialization_archives.run  
  
    set Path=D:\Boost\out\x64rel\boost\bin.v2\libs\serialization\build\msvc-14.1\release\asynch-exceptions-on\threading-multi;%Path%  
  
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_serialization_archives.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_serialization_archives.exe"   > "..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_serialization_archives.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_serialization_archives.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_serialization_archives.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_serialization_archives.output"  
    echo EXIT STATUS: %status% >> "..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_serialization_archives.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_serialization_archives.output"  
    if %status% EQU 0 (  
        copy "..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_serialization_archives.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_serialization_archives.output" "..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_serialization_archives.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_serialization_archives.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_serialization_archives.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_serialization_archives.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
====== BEGIN OUTPUT ======  
   
EXIT STATUS: 255   
====== END OUTPUT ======  
testing.capture-output ..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_num_facet_serialization.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_num_facet_serialization.run  
  
    set Path=D:\Boost\out\x64rel\boost\bin.v2\libs\serialization\build\msvc-14.1\release\asynch-exceptions-on\threading-multi;%Path%  
  
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_num_facet_serialization.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_num_facet_serialization.exe"   > "..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_num_facet_serialization.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_num_facet_serialization.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_num_facet_serialization.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_num_facet_serialization.output"  
    echo EXIT STATUS: %status% >> "..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_num_facet_serialization.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_num_facet_serialization.output"  
    if %status% EQU 0 (  
        copy "..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_num_facet_serialization.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_num_facet_serialization.output" "..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_num_facet_serialization.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_num_facet_serialization.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\out\x64rel\boost\bin.v2\libs\math\example\nonfinite_num_facet_serialization.test\msvc-14.1\release\asynch-exceptions-on\threading-multi\nonfinite_num_facet_serialization.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
====== BEGIN OUTPUT ======  
  
Nonfinite_serialization.cpp' example program.  
std::numeric_limits<float>::max_digits10 is 9  
std::numeric_limits<double>::max_digits10 is 17  
Construct some foo structures with a finite and nonfinites.  
f0 :   
|-- fvalue = 3.141593  
`-- dvalue = 3.14159265358979  
f1 :   
|-- fvalue = -inf  
`-- dvalue = -inf  
f2 :   
|-- fvalue = inf  
`-- dvalue = inf  
f3 :   
|-- fvalue = nan  
`-- dvalue = nan  
 Write to a string buffer.  
Output is: `(3.14159274,3.1415926535897931)(-inf,-inf)(inf,inf)(nan,nan)'  
Done output to ostringstream.  
Read foo structures from a string buffer.  
Input is: `(3.1415927,3.1415926535897931)(-inf,-inf)(inf,inf)(nan,nan)'  
Read OK.  
f0 :   
|-- fvalue = 3.141593  
`-- dvalue = 3.14159265358979  
f1 :   
|-- fvalue = -inf  
`-- dvalue = -inf  
f2 :   
|-- fvalue = inf  
`-- dvalue = inf  
f3 :   
|-- fvalue = nan  
`-- dvalue = nan  
Done input from istringstream.  
Serialize (using Boost text archive).  
f0 :   
|-- fvalue = 3.141593  
`-- dvalue = 3.14159265358979  
f1 :   
|-- fvalue = -inf  
`-- dvalue = -inf  
f2 :   
|-- fvalue = inf  
`-- dvalue = inf  
f3 :   
|-- fvalue = nan  
`-- dvalue = nan  
   
EXIT STATUS: 255   
====== END OUTPUT ======  
...failed updating 7 targets...

---

## Comment 1

> Username: pabristow  
> Created at: 2019-11-04 12:13:45 UTC  
> Url: https://github.com/boostorg/math/issues/268#issuecomment-549327531  

1  This appears to be caused by a change in namespace name in Boost.Test from unit_test_framework to unit_test.  It is now assumed that the BOOST_TEST_MESSAGE output is enabled by a run-time argument  
   --log_level="message".  
  
If you wish to confirm that the underlying lambert_w works OK, you can safely just delete the line   
  
oost::unit_test_framework::unit_test_log.set_threshold_level(boost::unit_test_framework::log_messages);  
  
2  The behaviour of Nan and Infinity on serialization and on input and output is entirely implementation defined.  
  
One might say that it is generally Bad/Undefined Behaviour :-(  
  
Your mileage WILL vary : you need to ensure that NaN and Infinity work as you wish on your platform both for input and output representations and for serialization.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-11-08 14:03:19 UTC  
> Url: https://github.com/boostorg/math/issues/268#issuecomment-551839306  

This has been fixed in Boost.Test: https://github.com/boostorg/test/commit/2f7bc05ba47b9e7b867c59e5ecbcc7e6ffc93835
