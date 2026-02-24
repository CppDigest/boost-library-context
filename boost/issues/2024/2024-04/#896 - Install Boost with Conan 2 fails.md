# #896 - Install Boost with Conan 2 fails [Closed]

> Username: FTamas77  
> Created at: 2024-04-30 00:02:45 UTC  
> Updated at: 2024-05-01 10:17:32 UTC  
> Closed at: 2024-05-01 10:17:32 UTC  
> Url: https://github.com/boostorg/boost/issues/896  

This is my Conan file:  
  
```  
[requires]  
boost/1.81.0  
  
[generators]  
CMakeDeps  
CMakeToolchain  
  
[options]  
```  
  
This is my Conan profile:  
  
```  
[settings]  
arch=x86_64  
build_type=Debug  
compiler=msvc  
compiler.cppstd=17  
compiler.runtime=dynamic  
compiler.version=193  
os=Windows  
```  
And the out is:  
  
```  
1> [CMake] ERROR: The system was unable to find the specified registry key or value.  
1> [CMake] Performing configuration checks  
1> [CMake]   
1> [CMake]     - default address-model    : 64-bit [1]  
1> [CMake]     - default architecture     : x86 [1]  
1> [CMake]     - compiler supports SSE2   : yes [2]  
1> [CMake]     - compiler supports SSE4.1 : yes [2]  
1> [CMake]     - has synchronization.lib  : yes [2]  
1> [CMake]     - has std::atomic_ref      : no [2]  
1> [CMake]     - has statx                : no [2]  
1> [CMake]     - has statx syscall        : no [2]  
1> [CMake]     - has BCrypt API           : yes [2]  
1> [CMake]     - has init_priority attribute : no [2]  
1> [CMake]     - has stat::st_blksize     : no [2]  
1> [CMake]     - has stat::st_mtim        : no [2]  
1> [CMake]     - has stat::st_mtimensec   : no [2]  
1> [CMake]     - has stat::st_mtimespec   : no [2]  
1> [CMake]     - has stat::st_birthtim    : no [2]  
1> [CMake]     - has stat::st_birthtimensec : no [2]  
1> [CMake]     - has stat::st_birthtimespec : no [2]  
1> [CMake]     - has fdopendir(O_NOFOLLOW) : no [2]  
1> [CMake]     - has POSIX *at APIs       : no [2]  
1> [CMake]     - cxx11_auto_declarations  : yes [2]  
1> [CMake]     - cxx11_constexpr          : yes [2]  
1> [CMake]     - cxx11_defaulted_functions : yes [2]  
1> [CMake]     - cxx11_final              : yes [2]  
1> [CMake]     - cxx11_hdr_mutex          : yes [2]  
1> [CMake]     - cxx11_hdr_tuple          : yes [2]  
1> [CMake]     - cxx11_lambdas            : yes [2]  
1> [CMake]     - cxx11_noexcept           : yes [2]  
1> [CMake]     - cxx11_nullptr            : yes [2]  
1> [CMake]     - cxx11_rvalue_references  : yes [2]  
1> [CMake]     - cxx11_template_aliases   : yes [2]  
1> [CMake]     - cxx11_thread_local       : yes [2]  
1> [CMake]     - cxx11_variadic_templates : yes [2]  
1> [CMake]     - has_icu builds           : no [2]  
1> [CMake]     - zlib                     : no [3]  
1> [CMake]     - bzip2                    : no [3]  
1> [CMake]     - cxx11_decltype           : yes [2]  
1> [CMake]     - cxx11_basic_alignas      : yes [2]  
1> [CMake]     - cxx11_defaulted_moves    : yes [2]  
1> [CMake]     - cxx11_hdr_functional     : yes [2]  
1> [CMake]     - cxx11_hdr_type_traits    : yes [2]  
1> [CMake]     - cxx11_override           : yes [2]  
1> [CMake]     - cxx11_range_based_for    : yes [2]  
1> [CMake]     - cxx11_scoped_enums       : yes [2]  
1> [CMake]     - cxx11_smart_ptr          : yes [2]  
1> [CMake]     - cxx11_static_assert      : yes [2]  
1> [CMake]     - native atomic int32 supported : yes [2]  
1> [CMake]     - has message compiler     : yes [2]  
1> [CMake]     - native syslog supported  : no [2]  
1> [CMake]     - pthread supports robust mutexes : no [2]  
1> [CMake]     - compiler supports SSSE3  : yes [2]  
1> [CMake]     - compiler supports AVX2   : yes [2]  
1> [CMake]     - gcc visibility           : no [2]  
1> [CMake]     - sfinae_expr              : yes [2]  
1> [CMake]     - cxx11_unified_initialization_syntax : yes [2]  
1> [CMake]     - cxx11_hdr_initializer_list : yes [2]  
1> [CMake]     - cxx11_hdr_chrono         : yes [2]  
1> [CMake]     - cxx11_numeric_limits     : yes [2]  
1> [CMake]     - cxx11_hdr_array          : yes [2]  
1> [CMake]     - cxx11_hdr_atomic         : yes [2]  
1> [CMake]     - cxx11_allocator          : yes [2]  
1> [CMake]     - cxx11_explicit_conversion_operators : yes [2]  
1> [CMake]     - long double support      : yes [2]  
1> [CMake]     - std::fstream is moveable and swappable : yes [2]  
1> [CMake]     - Has Large File Support   : yes [2]  
1> [CMake]     - Has attribute init_priority : no [2]  
1> [CMake]     - libbacktrace builds      : no [2]  
1> [CMake]     - libbacktrace builds      : no [4]  
1> [CMake]     - addr2line builds         : no [2]  
1> [CMake]     - addr2line builds         : no [4]  
1> [CMake]     - WinDbg builds            : no [2]  
1> [CMake]     - WinDbg builds            : no [4]  
1> [CMake]     - WinDbgCached builds      : no [2]  
1> [CMake]     - WinDbgCached builds      : no [4]  
1> [CMake]     - BOOST_COMP_GNUC >= 4.3.0 : no [2]  
1> [CMake]     - cxx11_hdr_thread         : yes [2]  
1> [CMake]     - cxx11_hdr_regex          : yes [2]  
1> [CMake]   
1> [CMake] boost/1.81.0: ERROR:   
1> [CMake] Package 'f1ebb80658fd9c40ef10124242805f23dc9e84f0' build failed  
1> [CMake] boost/1.81.0: WARN: Build folder C:\Users\xxx\.conan2\p\b\boost7c11c7a381aa2\b\build-debug  
1> [CMake] ERROR: boost/1.81.0: Error in build() method, line 1003  
1> [CMake] 	self.run(full_command)  
1> [CMake] 	ConanException: Error 1 while executing  
1> [CMake] CMake Error at D:\xxx\xxx\CMakeLists.txt:19 (include):  
1> [CMake]   include could not find requested file:  
1> [CMake]   
1> [CMake]     D:/xxxx/xxx/build/Debug/conan_toolchain.cmake  
1> [CMake]   
1> [CMake]   
1> [CMake] CMake Warning at C:/Program Files/Microsoft Visual Studio/2022/Professional/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.28/Modules/FindBoost.cmake:2233 (message):  
1> [CMake]   No header defined for Boost::math; skipping header check (note: header-only  
1> [CMake]   libraries have no designated component)  
1> [CMake] Call Stack (most recent call first):  
1> [CMake]   CMakeLists.txt:22 (find_package)  
1> [CMake] CMake Error at C:/Program Files/Microsoft Visual Studio/2022/Professional/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.28/Modules/FindPackageHandleStandardArgs.cmake:230 (message):  
1> [CMake]   Could NOT find Boost (missing: Boost_INCLUDE_DIR Boost::math)  
1> [CMake] Call Stack (most recent call first):  
1> [CMake]   C:/Program Files/Microsoft Visual Studio/2022/Professional/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.28/Modules/FindPackageHandleStandardArgs.cmake:600 (_FPHSA_FAILURE_MESSAGE)  
1> [CMake]   C:/Program Files/Microsoft Visual Studio/2022/Professional/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.28/Modules/FindBoost.cmake:2392 (find_package_handle_standard_args)  
1> [CMake]   CMakeLists.txt:22 (find_package)  
1> [CMake] -- Configuring incomplete, errors occurred!  
1> 'C:\WINDOWS\system32\cmd.exe' '/c "%SYSTEMROOT%\System32\chcp.com 65001 >NUL && "c:\program files\microsoft visual studio\2022\professional\common7\ide\commonextensions\microsoft\cmake\CMake\bin\cmake.exe"  -G "Visual Studio 17 2022" -A x64  -DCMAKE_CONFIGURATION_TYPES:STRING="Debug" -DCMAKE_INSTALL_PREFIX:PATH="D:\xxx\xxx\install\Debug"   "D:\xxx\xxx" 2>&1"' execution failed with error: ''C:\WINDOWS\system32\cmd.exe' '/c "%SYSTEMROOT%\System32\chcp.com 65001 >NUL && "c:\program files\microsoft visual studio\2022\professional\common7\ide\commonextensions\microsoft\cmake\CMake\bin\cmake.exe"  -G "Visual Studio 17 2022" -A x64  -DCMAKE_CONFIGURATION_TYPES:STRING="Debug" -DCMAKE_INSTALL_PREFIX:PATH="D:\xxx\xxx\install\Debug"   "D:\Projects\xxxx" 2>&1"' returned with exit code: 1'.  
```  
  
  
Yesterday, I could build in Release. But in the release, the header couldn't be found in my CMake. [This link](https://discourse.cmake.org/t/findboost-cmake-seems-to-work-but-adds-no-include-folders-to-compile-line-when-linked-against/7923) says, if I want to include Boost in my project, I need the debug build. Github Copilot says I should check that list. But should I install the dependency by myself in debug and not in release? I also tried different Boost versions.  
  
Further versions:  
  
- cmake version 3.29.1  
- Conan version 2.2.2  
- Visual Studio 2022
