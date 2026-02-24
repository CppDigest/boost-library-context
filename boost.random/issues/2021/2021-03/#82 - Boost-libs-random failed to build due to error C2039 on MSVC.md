# #82 - Boost\libs\random failed to build due to error C2039 on MSVC [Closed]

> Username: QuellaZhang  
> Created at: 2021-03-16 07:06:00 UTC  
> Updated at: 2024-02-14 07:32:26 UTC  
> Closed at: 2024-02-14 07:32:26 UTC  
> Url: https://github.com/boostorg/random/issues/82  

**Issue description:**  
Boost\libs\random failed to build due to error C2039 on MSVC. Could you please take a look? We ues https://github.com/boostorg/boost/commit/ea9f9fbf1b0fef4c8a9eefc2dc0f24319f83cf8b from Boost master branch.  
  
**Build step:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git boost  
2. open a VS 2019 x64 command prompt and browse to boost  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\random\test  
  
**Error info:**  
multiprecision_int_test.cpp  
libs\random\test\multiprecision_int_test.cpp(37): error C2039: 'list': is not a member of 'boost::mpl'  
.\boost/mpl/always.hpp(21): note: see declaration of 'boost::mpl'  
libs\random\test\multiprecision_int_test.cpp(37): error C2143: syntax error: missing ';' before '<'  
libs\random\test\multiprecision_int_test.cpp(37): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
libs\random\test\multiprecision_int_test.cpp(50): error C2065: 'engines': undeclared identifier  
libs\random\test\multiprecision_int_test.cpp(50): error C2923: 'boost::unit_test::ut_detail::template_test_case_gen': 'engines' is not a valid template type argument for parameter 'TestTypesList'  
libs\random\test\multiprecision_int_test.cpp(50): note: see declaration of 'engines'  
libs\random\test\multiprecision_int_test.cpp(50): error C2976: 'boost::unit_test::ut_detail::template_test_case_gen': too few template arguments  
.\boost/test/tree/test_case_template.hpp(155): note: see declaration of 'boost::unit_test::ut_detail::template_test_case_gen'  
libs\random\test\multiprecision_int_test.cpp(50): error C2440: '<function-style-cast>': cannot convert from 'initializer list' to 'boost::unit_test::ut_detail::template_test_case_gen'  
libs\random\test\multiprecision_int_test.cpp(50): note: No constructor could take the source type, or constructor overload resolution was ambiguous  
libs\random\test\multiprecision_int_test.cpp(50): error C2664: 'boost::unit_test::ut_detail::auto_test_unit_registrar::auto_test_unit_registrar(int)': cannot convert argument 1 from 'boost::unit_test::decorator::collector_t' to 'int'

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-03-16 10:48:19 UTC  
> Url: https://github.com/boostorg/random/issues/82#issuecomment-800153804  

Missing #includes, fixed by: https://github.com/boostorg/random/pull/83

---

## Comment 2

> Username: jwakely  
> Created at: 2021-09-01 17:06:15 UTC  
> Url: https://github.com/boostorg/random/issues/82#issuecomment-910480539  

This was broken in 1.76.0 but seems to be fixed in 1.77.0, even though #83 never got merged.  
  
I've included the unmerged pull request in the Fedora 1.76.0 package to fix a CI failure.

---

## Comment 3

> Username: QuellaZhang  
> Created at: 2022-06-01 06:19:00 UTC  
> Url: https://github.com/boostorg/random/issues/82#issuecomment-1143163039  

This issue still can be repro on [8bfc57e268c536f68d3832741bccdefad534d9d4](https://github.com/boostorg/boost/commit/8bfc57e268c536f68d3832741bccdefad534d9d4).  
  
After apply patch, both of multiprecision_int_test.exe and multiprecision_float_test.exe are crash.  
  
multiprecision_float_test.cpp  
testing.capture-output ..\out\x86rel_1\boost\bin.v2\libs\random\test\multiprecision_int_test.test\msvc-14.2\release\threading-multi\multiprecision_int_test.run  
====== BEGIN OUTPUT ======  
   
EXIT STATUS: -1073740791   
====== END OUTPUT ======  
  
    set Path=F:\gitP\boostorg\out\x86rel_1\boost\bin.v2\libs\test\build\msvc-14.2\release\threading-multi;%Path%  
  
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\out\x86rel_1\boost\bin.v2\libs\random\test\multiprecision_int_test.test\msvc-14.2\release\threading-multi\multiprecision_int_test.exe"   > "..\out\x86rel_1\boost\bin.v2\libs\random\test\multiprecision_int_test.test\msvc-14.2\release\threading-multi\multiprecision_int_test.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "..\out\x86rel_1\boost\bin.v2\libs\random\test\multiprecision_int_test.test\msvc-14.2\release\threading-multi\multiprecision_int_test.output"  
    echo EXIT STATUS: %status% >> "..\out\x86rel_1\boost\bin.v2\libs\random\test\multiprecision_int_test.test\msvc-14.2\release\threading-multi\multiprecision_int_test.output"  
    if %status% EQU 0 (  
        copy "..\out\x86rel_1\boost\bin.v2\libs\random\test\multiprecision_int_test.test\msvc-14.2\release\threading-multi\multiprecision_int_test.output" "..\out\x86rel_1\boost\bin.v2\libs\random\test\multiprecision_int_test.test\msvc-14.2\release\threading-multi\multiprecision_int_test.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\out\x86rel_1\boost\bin.v2\libs\random\test\multiprecision_int_test.test\msvc-14.2\release\threading-multi\multiprecision_int_test.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
...failed testing.capture-output ..\out\x86rel_1\boost\bin.v2\libs\random\test\multiprecision_int_test.test\msvc-14.2\release\threading-multi\multiprecision_int_test.run...

---

## Comment 4

> Username: jzmaddock  
> Created at: 2022-06-01 17:15:03 UTC  
> Url: https://github.com/boostorg/random/issues/82#issuecomment-1143897190  

Confirmed, I must have checked this when I filed the PR, so something must have changed... will investigate shortly.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2022-06-01 18:36:58 UTC  
> Url: https://github.com/boostorg/random/issues/82#issuecomment-1144000125  

There was a problem with the logic in the test cases, causes numeric overflow to occur.  Now fixed in the referenced PR.  
  
I see we have no CI for this library, so I'll submit a PR for that too later.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2022-06-01 19:00:29 UTC  
> Url: https://github.com/boostorg/random/issues/82#issuecomment-1144021291  

See also https://github.com/boostorg/random/pull/88
