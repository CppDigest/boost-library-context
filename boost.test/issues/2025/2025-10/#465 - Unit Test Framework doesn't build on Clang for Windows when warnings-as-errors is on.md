# #465 - Unit Test Framework doesn't build on Clang for Windows when warnings-as-errors is on [Closed]

> Username: pdimov  
> Created at: 2025-10-28 15:08:50 UTC  
> Updated at: 2025-10-29 07:50:13 UTC  
> Closed at: 2025-10-29 07:50:13 UTC  
> Url: https://github.com/boostorg/test/issues/465  

This is a problem, because libraries that use it in their test suites and enable warnings-as-errors=on (such as OpenMethod) can't do that for Clang for Windows.  
  
The warnings are:  
```  
compile-c-c++ ..\..\bin.v2\libs\test\build\clang-win-19.1.5\debug\x86_64\threading-multi\debug.obj  
In file included from C:\boost-git\develop\libs\test\src\debug.cpp:16:  
..\..\boost/test/impl/debug.ipp(143,11): error: cast from 'FARPROC' (aka 'long long (*)()') to 'int (*)()' converts to incompatible function type [-Werror,-Wcast-function-type-mismatch]  
  143 |     res = reinterpret_cast<T>( ::GetProcAddress( m, symbol_name ) );  
      |           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
```  
In file included from C:\boost-git\develop\libs\test\src\test_tools.cpp:16:  
..\..\boost/test/impl/test_tools.ipp(121,28): error: 'wcrtomb' is deprecated: This function or variable may be unsafe. Consider using wcrtomb_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Werror,-Wdeprecated-declarations]  
  121 |     std::size_t ret = std::wcrtomb(&mb[0], r, &state);  
      |                            ^  
```  
```  
compile-c-c++ ..\..\bin.v2\libs\test\build\clang-win-19.1.5\debug\x86_64\threading-multi\execution_monitor.obj  
In file included from C:\boost-git\develop\libs\test\src\execution_monitor.cpp:16:  
..\..\boost/test/impl/execution_monitor.ipp(1388,89): error: 'strerror' is deprecated: This function or variable may be unsafe. Consider using strerror_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Werror,-Wdeprecated-declarations]  
 1388 |                               "system_error produced by: %s: %s", ex.p_failed_exp, std::strerror( ex.p_errno ) ); }  
      |                                                                                         ^  
```  
```  
compile-c-c++ ..\..\bin.v2\libs\test\build\clang-win-19.1.5\debug\x86_64\threading-multi\unit_test_parameters.obj  
In file included from C:\boost-git\develop\libs\test\src\unit_test_parameters.cpp:16:  
In file included from ..\..\boost/test/impl/unit_test_parameters.ipp:40:  
..\..\boost/test/utils/runtime/env/fetch.hpp(44,23): error: 'getenv' is deprecated: This function or variable may be unsafe. Consider using _dupenv_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [-Werror,-Wdeprecated-declarations]  
   44 |     char const* res = getenv( var_name.begin() );  
      |                       ^  
```  
The last three are explicitly disabled in their corresponding source files, but only when `BOOST_MSVC` is defined. This macro is only defined for MSVC, not for Clang. `_MSC_VER` is defined for both.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-10-28 18:28:07 UTC  
> Url: https://github.com/boostorg/test/issues/465#issuecomment-3457907267  

https://github.com/boostorg/test/pull/466
