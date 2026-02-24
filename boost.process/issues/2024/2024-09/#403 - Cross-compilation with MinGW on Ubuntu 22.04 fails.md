# #403 - Cross-compilation with MinGW on Ubuntu 22.04 fails [Open]

> Username: vejbomar  
> Created at: 2024-09-26 13:54:16 UTC  
> Updated at: 2024-09-26 13:54:16 UTC  
> Url: https://github.com/boostorg/process/issues/403  

When building `Process` module (`develop` branch) on Ubuntu 22.04 from WSL on Windows machine with these commands:  
  
```  
git clone --recursive https://github.com/boostorg/boost.git boost-build-process  
cd boost-build-process  
git checkout develop  
git submodule update --init --recursive  
  
echo "using gcc :  : x86_64-w64-mingw32-g++ ;" > "user-config-x64.jam"  
  
./bootstrap.sh  
./b2 --with-process --user-config=./user-config-x64.jam --prefix=./boost-x64 target-os=windows address-model=64 variant=debug --debug-configuration install  
```  
  
I get the following errors:  
  
```  
gcc.compile.c++ bin.v2/libs/process/build/gcc-10/debug/x86_64/boost.process.fs-boost/link-static/target-os-windows/threadapi-win32/threading-multi/visibility-hidden/ext/exe.o  
/home/vejby/boost-build-process/libs/process/src/ext/exe.cpp: In function ‘boost::filesystem::path boost::process::v2::ext::exe(HANDLE, boost::system::error_code&)’:  
/home/vejby/boost-build-process/libs/process/src/ext/exe.cpp:78:9: error: ‘QueryFullProcessImageNameW’ was not declared in this scope  
   78 |     if (QueryFullProcessImageNameW(proc, 0, buffer, &size))  
      |         ^~~~~~~~~~~~~~~~~~~~~~~~~~  
  
    "x86_64-w64-mingw32-g++"   -fvisibility-inlines-hidden -m64 -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_FILESYSTEM_NO_LIB=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DBOOST_REGEX_NO_LIB=1 -DBOOST_SYSTEM_NO_LIB=1 -DBOOST_SYSTEM_STATIC_LINK=1 -DWIN32_LEAN_AND_MEAN   -I"." -I"/home/vejby/boost-build-process/libs/algorithm/include" -I"/home/vejby/boost-build-process/libs/assert/include" -I"/home/vejby/boost-build-process/libs/bind/include" -I"/home/vejby/boost-build-process/libs/container_hash/include" -I"/home/vejby/boost-build-process/libs/core/include" -I"/home/vejby/boost-build-process/libs/describe/include" -I"/home/vejby/boost-build-process/libs/detail/include" -I"/home/vejby/boost-build-process/libs/filesystem/include" -I"/home/vejby/boost-build-process/libs/function/include" -I"/home/vejby/boost-build-process/libs/function_types/include" -I"/home/vejby/boost-build-process/libs/fusion/include" -I"/home/vejby/boost-build-process/libs/iterator/include" -I"/home/vejby/boost-build-process/libs/move/include" -I"/home/vejby/boost-build-process/libs/mp11/include" -I"/home/vejby/boost-build-process/libs/optional/include" -I"/home/vejby/boost-build-process/libs/predef/include" -I"/home/vejby/boost-build-process/libs/process/include" -I"/home/vejby/boost-build-process/libs/regex/include" -I"/home/vejby/boost-build-process/libs/scope/include" -I"/home/vejby/boost-build-process/libs/smart_ptr/include" -I"/home/vejby/boost-build-process/libs/system/include" -I"/home/vejby/boost-build-process/libs/throw_exception/include" -I"/home/vejby/boost-build-process/libs/tuple/include" -I"/home/vejby/boost-build-process/libs/type_index/include" -I"/home/vejby/boost-build-process/libs/type_traits/include" -I"/home/vejby/boost-build-process/libs/unordered/include" -I"/home/vejby/boost-build-process/libs/variant2/include" -I"/home/vejby/boost-build-process/libs/winapi/include"  -c -o "bin.v2/libs/process/build/gcc-10/debug/x86_64/boost.process.fs-boost/link-static/target-os-windows/threadapi-win32/threading-multi/visibility-hidden/ext/exe.o" "/home/vejby/boost-build-process/libs/process/src/ext/exe.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/process/build/gcc-10/debug/x86_64/boost.process.fs-boost/link-static/target-os-windows/threadapi-win32/threading-multi/visibility-hidden/ext/exe.o...  
```  
  
```  
gcc.compile.c++ bin.v2/libs/process/build/gcc-10/debug/x86_64/boost.process.fs-boost/link-static/target-os-windows/threadapi-win32/threading-multi/visibility-hidden/windows/default_launcher.o  
In file included from /home/vejby/boost-build-process/libs/process/src/windows/default_launcher.cpp:16:  
./boost/process/v2/windows/default_launcher.hpp:220:3: error: ‘STARTUPINFOEXW’ does not name a type; did you mean ‘STARTUPINFOW’?  
  220 |   STARTUPINFOEXW startup_info{{sizeof(STARTUPINFOEXW), nullptr, nullptr, nullptr,  
      |   ^~~~~~~~~~~~~~  
      |   STARTUPINFOW  
./boost/process/v2/windows/default_launcher.hpp: In member function ‘boost::process::v2::windows::default_launcher::enable_init<Executor, Inits ...> boost::process::v2::windows::default_launcher::operator()(Executor, boost::system::error_code&, const typename std::enable_if<(boost::asio::execution::is_executor<T>::value || boost::asio::is_executor<Executor>::value), boost::filesystem::path>::type&, Args&&, Inits&& ...)’:  
./boost/process/v2/windows/default_launcher.hpp:312:10: error: ‘startup_info’ was not declared in this scope  
  312 |         &startup_info.StartupInfo,  
      |          ^~~~~~~~~~~~  
/home/vejby/boost-build-process/libs/process/src/windows/default_launcher.cpp: In static member function ‘static std::size_t boost::process::v2::windows::default_launcher::escape_argv_string(wchar_t*, std::size_t, boost::basic_string_view<wchar_t, std::char_traits<wchar_t> >)’:  
/home/vejby/boost-build-process/libs/process/src/windows/default_launcher.cpp:64:16: warning: unused variable ‘end’ [-Wunused-variable]  
   64 |     const auto end = itr + sz;  
      |                ^~~  
  
    "x86_64-w64-mingw32-g++"   -fvisibility-inlines-hidden -m64 -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_FILESYSTEM_NO_LIB=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DBOOST_REGEX_NO_LIB=1 -DBOOST_SYSTEM_NO_LIB=1 -DBOOST_SYSTEM_STATIC_LINK=1 -DWIN32_LEAN_AND_MEAN   -I"." -I"/home/vejby/boost-build-process/libs/algorithm/include" -I"/home/vejby/boost-build-process/libs/assert/include" -I"/home/vejby/boost-build-process/libs/bind/include" -I"/home/vejby/boost-build-process/libs/container_hash/include" -I"/home/vejby/boost-build-process/libs/core/include" -I"/home/vejby/boost-build-process/libs/describe/include" -I"/home/vejby/boost-build-process/libs/detail/include" -I"/home/vejby/boost-build-process/libs/filesystem/include" -I"/home/vejby/boost-build-process/libs/function/include" -I"/home/vejby/boost-build-process/libs/function_types/include" -I"/home/vejby/boost-build-process/libs/fusion/include" -I"/home/vejby/boost-build-process/libs/iterator/include" -I"/home/vejby/boost-build-process/libs/move/include" -I"/home/vejby/boost-build-process/libs/mp11/include" -I"/home/vejby/boost-build-process/libs/optional/include" -I"/home/vejby/boost-build-process/libs/predef/include" -I"/home/vejby/boost-build-process/libs/process/include" -I"/home/vejby/boost-build-process/libs/regex/include" -I"/home/vejby/boost-build-process/libs/scope/include" -I"/home/vejby/boost-build-process/libs/smart_ptr/include" -I"/home/vejby/boost-build-process/libs/system/include" -I"/home/vejby/boost-build-process/libs/throw_exception/include" -I"/home/vejby/boost-build-process/libs/tuple/include" -I"/home/vejby/boost-build-process/libs/type_index/include" -I"/home/vejby/boost-build-process/libs/type_traits/include" -I"/home/vejby/boost-build-process/libs/unordered/include" -I"/home/vejby/boost-build-process/libs/variant2/include" -I"/home/vejby/boost-build-process/libs/winapi/include"  -c -o "bin.v2/libs/process/build/gcc-10/debug/x86_64/boost.process.fs-boost/link-static/target-os-windows/threadapi-win32/threading-multi/visibility-hidden/windows/default_launcher.o" "/home/vejby/boost-build-process/libs/process/src/windows/default_launcher.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/process/build/gcc-10/debug/x86_64/boost.process.fs-boost/link-static/target-os-windows/threadapi-win32/threading-multi/visibility-hidden/windows/default_launcher.o...  
```  
  
```  
gcc.compile.c++ bin.v2/libs/process/build/gcc-10/debug/x86_64/boost.process.fs-boost/link-static/target-os-windows/threadapi-win32/threading-multi/visibility-hidden/environment.o  
In file included from ./boost/process/v2/default_launcher.hpp:17,  
                 from /home/vejby/boost-build-process/libs/process/src/environment.cpp:13:  
./boost/process/v2/windows/default_launcher.hpp:220:3: error: ‘STARTUPINFOEXW’ does not name a type; did you mean ‘STARTUPINFOW’?  
  220 |   STARTUPINFOEXW startup_info{{sizeof(STARTUPINFOEXW), nullptr, nullptr, nullptr,  
      |   ^~~~~~~~~~~~~~  
      |   STARTUPINFOW  
./boost/process/v2/windows/default_launcher.hpp: In member function ‘boost::process::v2::windows::default_launcher::enable_init<Executor, Inits ...> boost::process::v2::windows::default_launcher::operator()(Executor, boost::system::error_code&, const typename std::enable_if<(boost::asio::execution::is_executor<T>::value || boost::asio::is_executor<Executor>::value), boost::filesystem::path>::type&, Args&&, Inits&& ...)’:  
./boost/process/v2/windows/default_launcher.hpp:312:10: error: ‘startup_info’ was not declared in this scope  
  312 |         &startup_info.StartupInfo,  
      |          ^~~~~~~~~~~~  
  
    "x86_64-w64-mingw32-g++"   -fvisibility-inlines-hidden -m64 -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_FILESYSTEM_NO_LIB=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_PROCESS_SOURCE=1 -DBOOST_REGEX_NO_LIB=1 -DBOOST_SYSTEM_NO_LIB=1 -DBOOST_SYSTEM_STATIC_LINK=1 -DWIN32_LEAN_AND_MEAN   -I"." -I"/home/vejby/boost-build-process/libs/algorithm/include" -I"/home/vejby/boost-build-process/libs/assert/include" -I"/home/vejby/boost-build-process/libs/bind/include" -I"/home/vejby/boost-build-process/libs/container_hash/include" -I"/home/vejby/boost-build-process/libs/core/include" -I"/home/vejby/boost-build-process/libs/describe/include" -I"/home/vejby/boost-build-process/libs/detail/include" -I"/home/vejby/boost-build-process/libs/filesystem/include" -I"/home/vejby/boost-build-process/libs/function/include" -I"/home/vejby/boost-build-process/libs/function_types/include" -I"/home/vejby/boost-build-process/libs/fusion/include" -I"/home/vejby/boost-build-process/libs/iterator/include" -I"/home/vejby/boost-build-process/libs/move/include" -I"/home/vejby/boost-build-process/libs/mp11/include" -I"/home/vejby/boost-build-process/libs/optional/include" -I"/home/vejby/boost-build-process/libs/predef/include" -I"/home/vejby/boost-build-process/libs/process/include" -I"/home/vejby/boost-build-process/libs/regex/include" -I"/home/vejby/boost-build-process/libs/scope/include" -I"/home/vejby/boost-build-process/libs/smart_ptr/include" -I"/home/vejby/boost-build-process/libs/system/include" -I"/home/vejby/boost-build-process/libs/throw_exception/include" -I"/home/vejby/boost-build-process/libs/tuple/include" -I"/home/vejby/boost-build-process/libs/type_index/include" -I"/home/vejby/boost-build-process/libs/type_traits/include" -I"/home/vejby/boost-build-process/libs/unordered/include" -I"/home/vejby/boost-build-process/libs/variant2/include" -I"/home/vejby/boost-build-process/libs/winapi/include"  -c -o "bin.v2/libs/process/build/gcc-10/debug/x86_64/boost.process.fs-boost/link-static/target-os-windows/threadapi-win32/threading-multi/visibility-hidden/environment.o" "/home/vejby/boost-build-process/libs/process/src/environment.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/process/build/gcc-10/debug/x86_64/boost.process.fs-boost/link-static/target-os-windows/threadapi-win32/threading-multi/visibility-hidden/environment.o...  
```  
  
Full logs: [x64_boost_build.log](https://github.com/user-attachments/files/17149741/x64_boost_build.log)  
  
As suggested in [mailing list](https://lists.boost.org/Archives/boost/2024/09/257935.php), I tested building while setting `_WIN32_WINNT` to higher version and it seemed to build fine afterwards. From the testing, it seems that `Process` needs at least `_WIN32_WINNT=0x0600` (Windows Vista) and `x86_64-w64-mingw32-g++` from Ubuntu 22.04 sets `_WIN32_WINNT=0x0502` (Windows XP SP2).  
  
Would it make sense to set such requirement in the `Process` module itself?
