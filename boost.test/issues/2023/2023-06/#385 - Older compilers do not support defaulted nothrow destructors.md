# #385 - Older compilers do not support defaulted nothrow destructors [Closed]

> Username: Lastique  
> Created at: 2023-06-13 11:39:19 UTC  
> Updated at: 2023-06-16 07:37:50 UTC  
> Closed at: 2023-06-16 07:37:50 UTC  
> Url: https://github.com/boostorg/test/issues/385  

It seems, https://github.com/boostorg/test/pull/375 broke compilation on MSVC-12, as seen in [Boost.Log CI](https://ci.appveyor.com/project/Lastique/log/build/job/p2m9fwlq5nkgn2gi#L562).  
  
```  
.\boost/test/utils/runtime/errors.hpp(42) : error C2610: 'boost::runtime::param_error::~param_error(void) throw()' : is not a special member function which can be defaulted  
        exception specification is not allowed  
.\boost/test/utils/runtime/errors.hpp(61) : error C2610: 'boost::runtime::init_error::~init_error(void) throw()' : is not a special member function which can be defaulted  
        exception specification is not allowed  
.\boost/test/utils/runtime/errors.hpp(67) : error C2610: 'boost::runtime::input_error::~input_error(void) throw()' : is not a special member function which can be defaulted  
        exception specification is not allowed  
.\boost/test/utils/runtime/errors.hpp(76) : error C2610: 'boost::runtime::specific_param_error<Derived,Base>::~specific_param_error(void) throw()' : is not a special member function which can be defaulted  
        exception specification is not allowed  
        .\boost/test/utils/runtime/errors.hpp(127) : see reference to class template instantiation 'boost::runtime::specific_param_error<Derived,Base>' being compiled  
.\boost/test/utils/runtime/errors.hpp(169) : error C2610: 'boost::runtime::ambiguous_param::~ambiguous_param(void) throw()' : is not a special member function which can be defaulted  
        exception specification is not allowed  
.\boost/test/utils/runtime/errors.hpp(185) : error C2610: 'boost::runtime::unrecognized_param::~unrecognized_param(void) throw()' : is not a special member function which can be defaulted  
        exception specification is not allowed  
    call "bin.v2\standalone\msvc\msvc-12.0\msvc-setup.bat" amd64 >nul  
 cl /Zm800 -nologo "libs\test\src\compiler_log_formatter.cpp" -c -Fo"bin.v2\libs\test\build\msvc-12.0\release\threading-multi\compiler_log_formatter.obj"     -TP /wd4675 /EHs /GR /O2 /Ob2 /W4 /MD /Zc:forScope /Zc:wchar_t /Zc:inline /Gw /favor:blend -wd4275 -wd4671 -wd4673 -DBOOST_ALL_NO_LIB=1 -DBOOST_TEST_DYN_LINK=1 -DNDEBUG "-I."   
```  
  
CC @ecatmur.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-06-13 11:49:15 UTC  
> Url: https://github.com/boostorg/test/issues/385#issuecomment-1589146276  

And the same issue is with [gcc-4.6](https://github.com/boostorg/log/actions/runs/5254355343/jobs/9492854724#step:8:13057):  
  
```  
./boost/test/utils/runtime/errors.hpp:42:5: error: function 'boost::runtime::param_error::~param_error()' defaulted on its first declaration must not have an exception-specification  
./boost/test/utils/runtime/errors.hpp:61:5: error: function 'boost::runtime::init_error::~init_error()' defaulted on its first declaration must not have an exception-specification  
./boost/test/utils/runtime/errors.hpp:67:5: error: function 'boost::runtime::input_error::~input_error()' defaulted on its first declaration must not have an exception-specification  
./boost/test/utils/runtime/errors.hpp:76:5: error: function 'boost::runtime::specific_param_error<Derived, Base>::~specific_param_error()' defaulted on its first declaration must not have an exception-specification  
./boost/test/utils/runtime/errors.hpp:169:5: error: function 'boost::runtime::ambiguous_param::~ambiguous_param()' defaulted on its first declaration must not have an exception-specification  
./boost/test/utils/runtime/errors.hpp:185:5: error: function 'boost::runtime::unrecognized_param::~unrecognized_param()' defaulted on its first declaration must not have an exception-specification  
  
    "g++-4.6"  -ftemplate-depth-1024 -std=c++0x -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -Wno-variadic-macros -DBOOST_ALL_NO_LIB=1 -DBOOST_TEST_DYN_LINK=1   -I"."  -c -o "bin.v2/libs/test/build/gcc-4.6/debug/cxxstd-0x-iso/threading-multi/visibility-hidden/compiler_log_formatter.o" "libs/test/src/compiler_log_formatter.cpp"  
```

---

## Comment 2

> Username: ecatmur  
> Created at: 2023-06-14 06:05:17 UTC  
> Url: https://github.com/boostorg/test/issues/385#issuecomment-1590527836  

Ugh, sorry. I can't see an elegant (one-line) fix; would a multi-line compatibility change be OK? Something like  
```c++  
#ifdef BOOST_NO_CXX11  
~param_error() BOOST_NOEXCEPT_OR_NOTHROW BOOST_OVERRIDE {}  
#else  
~param_error() noexcept override = default;  
#endif  
```  
If so (or if you can suggest any other mechanism) I'll prepare a PR.

---

## Comment 3

> Username: mborland  
> Created at: 2023-06-14 07:44:43 UTC  
> Url: https://github.com/boostorg/test/issues/385#issuecomment-1590653360  

> Ugh, sorry. I can't see an elegant (one-line) fix; would a multi-line compatibility change be OK? Something like  
>   
> ```c++  
>   
> #ifdef BOOST_NO_CXX11  
>   
> ~param_error() BOOST_NOEXCEPT_OR_NOTHROW BOOST_OVERRIDE {}  
>   
> #else  
>   
> ~param_error() noexcept override = default;  
>   
> #endif  
>   
> ```  
>   
> If so (or if you can suggest any other mechanism) I'll prepare a PR.  
  
That looks fine to me, and a PR would be great. Thanks.

---

## Comment 4

> Username: Lastique  
> Created at: 2023-06-14 08:27:17 UTC  
> Updated at: 2023-06-14 08:32:32 UTC  
> Url: https://github.com/boostorg/test/issues/385#issuecomment-1590720819  

> Ugh, sorry. I can't see an elegant (one-line) fix; would a multi-line compatibility change be OK? Something like  
>   
> ```c++  
> #ifdef BOOST_NO_CXX11  
> ~param_error() BOOST_NOEXCEPT_OR_NOTHROW BOOST_OVERRIDE {}  
> #else  
> ~param_error() noexcept override = default;  
> #endif  
> ```  
>   
> If so (or if you can suggest any other mechanism) I'll prepare a PR.  
  
You could define your own compatibility macro like `BOOST_DEFAULTED_FUNCTION`, which expands to `func() = default` only on compilers that fully support the use cases you need. That is, on gcc >= 4.7 and MSVC >= 14.0. Then the rest of the changes would be more clean.
