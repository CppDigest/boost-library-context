# #59 - The appveyor build scripts do not allow for preprocessor definitions in bjam's preferred manner [Closed]

> Username: jeking3  
> Created at: 2017-09-27 19:03:09 UTC  
> Updated at: 2021-01-06 04:20:06 UTC  
> Closed at: 2021-01-06 04:20:06 UTC  
> Url: https://github.com/boostorg/predef/issues/59  

The script ``https://raw.githubusercontent.com/boostorg/regression/develop/ci/src/ci_boost_library_test.py`` is used with every appveyor build job, and there is no facility to pass "define" statements down to bjam from the appveyor script.  
  
In one pull request I had to approximate it by setting preprocessor definitions in the CXXFLAGS setting, however this is only good for C++ and not for C, and the unit tests show preprocessor output for both C and C++.  
  
It would be nice to be able to do something like this in appveyor:  
```  
  - TOOLSET: mingw64-6  
    COMMENT: UWP DESKTOP  
    DEFINE: _WIN32_WINNT=0x0602; WINAPI_FAMILY=WINAPI_FAMILY_DESKTOP_APP  
    platform: 64  
    APPVEYOR_BUILD_WORKER_IMAGE: Visual Studio 2015  
```  
  
This should turn into:  
```  
b2 toolset=... address-model=... define="_WIN32_WINNT=0x0602" define="WINAPI_FAMILY=WINAPI_FAMILY_DESKTOP_APP" ...  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2018-05-13 17:49:21 UTC  
> Url: https://github.com/boostorg/predef/issues/59#issuecomment-388644013  

The ci/ scripts from uuid/format/date_time address this issue.
