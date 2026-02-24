# #209 - boost 1.83.0 on macOS Sonoma doesn't build boost_locale.a [Closed]

> Username: pisajew  
> Created at: 2023-11-28 14:35:23 UTC  
> Updated at: 2023-11-29 15:20:14 UTC  
> Closed at: 2023-11-29 10:16:40 UTC  
> Url: https://github.com/boostorg/locale/issues/209  

I'm building on a silicon Mac and cross-compiling to x86_64 (however tried with arm target, and the result is the same). Output of b2 command:  
```  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
  
Building the Boost C++ Libraries.  
  
  
    - compiler supports SSE2   : yes [2]  
    - compiler supports SSE4.1 : yes [2]  
    - has std::atomic_ref      : no [2]  
    - has statx                : no [2]  
    - has statx syscall        : no [2]  
    - has init_priority attribute : yes [2]  
    - has stat::st_blksize     : no [2]  
    - has stat::st_mtim        : no [2]  
    - has stat::st_mtimensec   : no [2]  
    - has stat::st_mtimespec   : yes [2]  
    - has stat::st_birthtim    : no [2]  
    - has stat::st_birthtimensec : no [2]  
    - has stat::st_birthtimespec : yes [2]  
    - has fdopendir(O_NOFOLLOW) : yes [2]  
    - has dirent::d_type       : yes [2]  
    - has POSIX *at APIs       : no [2]  
    - cxx11_auto_declarations  : no [2]  
    - cxx11_auto_declarations  : no [3]  
    - has_icu builds           : no [2]  
warning: Graph library does not contain MPI-based parallel components.  
note: to enable them, add "using mpi ;" to your user-config.jam.  
note: to suppress this message, pass "--without-graph_parallel" to bjam.  
    - zlib                     : yes (cached) [4]  
    - bzip2                    : yes (cached) [4]  
    - lzma                     : no  (cached) [4]  
    - zstd                     : no  (cached) [4]  
    - has_lzma_cputhreads builds : no [2]  
    - cxx11_constexpr          : no [2]  
    - cxx11_constexpr          : no [3]  
    - iconv (libc)             : no [2]  
    - iconv (separate)         : yes [2]  
    - icu                      : no [2]  
    - iconv (libc)             : no [3]  
    - iconv (separate)         : yes [3]  
    - icu                      : no [3]  
    - native atomic int32 supported : yes [2]  
    - native syslog supported  : yes [2]  
    - pthread supports robust mutexes : no [2]  
    - lockfree boost::atomic_flag : yes [2]  
    - compiler supports SSSE3  : yes [2]  
    - compiler supports AVX2   : yes [2]  
    - gcc visibility           : yes [2]  
    - cxx11_noexcept           : no [2]  
    - gcc visibility           : yes [3]  
    - cxx11_noexcept           : no [3]  
warning: skipping optional Message Passing Interface (MPI) library.  
note: to enable MPI support, add "using mpi ;" to user-config.jam.  
note: to suppress this message, pass "--without-mpi" to bjam.  
note: otherwise, you can safely ignore this message.  
warning: No python installation configured and autoconfiguration  
note: failed.  See http://www.boost.org/libs/python/doc/building.html  
note: for configuration instructions or pass --without-python to  
note: suppress this message and silently skip all Boost.Python targets  
    - libbacktrace builds      : no [2]  
    - libbacktrace builds      : no [3]  
    - addr2line builds         : yes [2]  
    - WinDbg builds            : no [2]  
    - WinDbg builds            : no [3]  
    - WinDbgCached builds      : no [2]  
    - WinDbgCached builds      : no [3]  
    - BOOST_COMP_GNUC >= 4.3.0 : no [2]  
  
[1] clang-15  
[2] clang-darwin-15/release/boost.locale.posix-on/link-static/threading-multi/visibility-hidden  
[3] clang-darwin-15/release/boost.locale.posix-on/build-no/link-static/threading-multi/visibility-hidden  
[4] link-static  
  
Component configuration:  
  
    - atomic                   : building  
    - chrono                   : building  
    - container                : building  
    - context                  : building  
    - contract                 : building  
    - coroutine                : building  
    - date_time                : building  
    - exception                : building  
    - fiber                    : building  
    - filesystem               : building  
    - graph                    : building  
    - graph_parallel           : building  
    - headers                  : building  
    - iostreams                : building  
    - json                     : building  
    - locale                   : building  
    - log                      : building  
    - math                     : building  
    - mpi                      : building  
    - nowide                   : building  
    - program_options          : building  
    - python                   : building  
    - random                   : building  
    - regex                    : building  
    - serialization            : building  
    - stacktrace               : building  
    - system                   : building  
    - test                     : building  
    - thread                   : building  
    - timer                    : building  
    - type_erasure             : building  
    - url                      : building  
    - wave                     : building  
  
...patience...  
...patience...  
...patience...  
...patience...  
...found 11003 targets...  
  
  
The Boost C++ Libraries were successfully built!  
```  
  
but there is no libboost_locale.a in staging.

---

## Comment 1

> Username: gh-user-2022  
> Created at: 2023-11-28 15:26:03 UTC  
> Url: https://github.com/boostorg/locale/issues/209#issuecomment-1830071361  

Noticed the same issue today on CentOS 6.  
  
Worked around by patching file "libs/locale/build/Jamfile.v2", changing line  
  
`exe has_iconv : $(TOP)/build/has_iconv.cpp ;`  
  
to  
  
`obj has_iconv : $(TOP)/build/has_iconv.cpp ;`

---

## Comment 2

> Username: Flamefire  
> Created at: 2023-11-28 19:35:43 UTC  
> Url: https://github.com/boostorg/locale/issues/209#issuecomment-1830546150  

> Noticed the same issue today on CentOS 6.  
>   
> Worked around by patching file "libs/locale/build/Jamfile.v2", changing line  
  
That is likely a different issue then as in the initial report iconv is found: `- iconv (separate)         : yes [2]`     
With your workaround you basically skip testing if it requires linking against libiconv. I Suspect for you both `iconv (separate)` and `iconv (libc)` are "no"? You might need to set `ICONV_PATH` as documented.  
  
@pisajew Looks like the compiler you are using is lacking C++11 support. See e.g. `cxx11_noexcept           : no`. Check the `config.log` as this looks odd as appearently Clang 15 is used which should default to a higher standard.

---

## Comment 3

> Username: gh-user-2022  
> Created at: 2023-11-29 08:57:08 UTC  
> Url: https://github.com/boostorg/locale/issues/209#issuecomment-1831478656  

@Flamefire  
  
I checked, the linking is OK: if I build it manually with `gcc has_iconv.cpp -o has_iconv.cpp.exe`, it gets the symbol `iconv_open` from `/lib64/libc.so.6`. I wanted to debug what happens inside the failing command `configure.builds has_iconv : $(properties) : "iconv (libc)"`, but I couldn't figure out how can I do that. (Can you advice how to do that?)  
  
I can submit a separate issue for this if preferred.

---

## Comment 4

> Username: pisajew  
> Created at: 2023-11-29 09:07:24 UTC  
> Url: https://github.com/boostorg/locale/issues/209#issuecomment-1831494337  

Strange indeed. If I checkout master boost_locale builds correctly. It is just for version tag 1.83.0 where it's not being built (sad as I need that one compiled). For version 1.83.0 here is the relevant output from config.log:  
```  
clang-darwin.compile.c++ ../build/boost/bin.v2/libs/config/checks/clang-darwin-15/release/link-static/threading-multi/visibility-hidden/cxx11_noexcept.o  
  
    "clang++"   -fvisibility-inlines-hidden -O3 -Wall -fvisibility=hidden -Wno-inline --target=arm64-apple-darwin  -DBOOST_ALL_NO_LIB=1 -DNDEBUG -DTEST_BOOST_NO_CXX11_NOEXCEPT   -I"."  -c -o "../build/boost/bin.v2/libs/config/checks/clang-darwin-15/release/link-static/threading-multi/visibility-hidden/cxx11_noexcept.o" "libs/config/checks/test_case.cpp"  
  
libs/config/checks/test_case.cpp:901:7: error: "Defect macro BOOST_NO_CXX11_NOEXCEPT is defined."  
#     error "Defect macro BOOST_NO_CXX11_NOEXCEPT is defined."  
      ^  
1 error generated.  
...failed updating 1 target...  
```  
  
but if I rerun the command manually adding correct standard option, it works:  
```  
% "clang++"   -fvisibility-inlines-hidden -O3 -Wall -fvisibility=hidden -Wno-inline --target=arm64-apple-darwin  -DBOOST_ALL_NO_LIB=1 -DNDEBUG -DTEST_BOOST_NO_CXX11_NOEXCEPT   -I"."  -c -o "../build/boost/bin.v2/libs/config/checks/clang-darwin-15/release/link-static/threading-multi/visibility-hidden/cxx11_noexcept.o" "libs/config/checks/test_case.cpp"            
libs/config/checks/test_case.cpp:901:7: error: "Defect macro BOOST_NO_CXX11_NOEXCEPT is defined."  
#     error "Defect macro BOOST_NO_CXX11_NOEXCEPT is defined."  
      ^  
1 error generated.  
% "clang++" -std=c++11  -fvisibility-inlines-hidden -O3 -Wall -fvisibility=hidden -Wno-inline --target=arm64-apple-darwin  -DBOOST_ALL_NO_LIB=1 -DNDEBUG -DTEST_BOOST_NO_CXX11_NOEXCEPT   -I"."  -c -o "../build/boost/bin.v2/libs/config/checks/clang-darwin-15/release/link-static/threading-multi/visibility-hidden/cxx11_noexcept.o" "libs/config/checks/test_case.cpp"  
%  
```

---

## Comment 5

> Username: pisajew  
> Created at: 2023-11-29 10:16:40 UTC  
> Url: https://github.com/boostorg/locale/issues/209#issuecomment-1831603707  

I've ended up manually specifying `-std=c++11` for cxxflags in project-config.jam, which does the job for me. As the problem doesn't exist on master I think this workaround is good enough and I'm closing this issue now.

---

## Comment 6

> Username: Flamefire  
> Created at: 2023-11-29 15:20:13 UTC  
> Url: https://github.com/boostorg/locale/issues/209#issuecomment-1832101260  

Ok so I guess that Clang is older and/or has C++98 as the default standard.  
  
Note that you can pass `cxxstd=11` to `b2` instead
