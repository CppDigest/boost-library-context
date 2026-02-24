# #749 - MSYS2/Mingw-w64: --no-cmake-config causes fiber and stacktrace to be ignored by the build system, no reason shown [Closed]

> Username: oscarfv  
> Created at: 2021-12-12 09:11:57 UTC  
> Updated at: 2021-12-12 15:05:57 UTC  
> Closed at: 2021-12-12 15:05:56 UTC  
> Url: https://github.com/boostorg/build/issues/749  

With 1.78 the same build process that previously worked correctly now doesn't build boost_fiber nor boost_stacktrace. The build does not fail, they are simply ignored.  
  
We are using gcc 11.2.  
  
This are the arguments passed to b2 (this sample only builds boost_fiber):  
  
```  
address-model=64 \  
    --with-fiber \  
    link=shared,static \  
    pch=off \  
    runtime-link=shared \  
    threading=multi \  
    threadapi=win32 \  
    toolset=gcc \  
    variant=release \  
    python=${_pyver} \  
    --debug-configuration \  
    --prefix=${pkgdir}${MINGW_PREFIX} \  
    --layout=tagged \  
    --no-cmake-config \  
    -sHAVE_ICU=1 \  
    -sICU_LINK=\"-L${MINGW_PREFIX}/lib -licuuc -licuin -licudt\" \  
    -sICU_PATH=${MINGW_PREFIX} \  
    -sICU_ICUUC_NAME=icuuc \  
    -sICU_ICUDT_NAME=icudt \  
    -sICU_ICUIN_NAME=icuin \  
    -sICONV_PATH=${MINGW_PREFIX} \  
    -sICONV_LINK=\"-L${MINGW_PREFIX}/lib -liconv\" \  
    -sNO_BZIP2 \  
    -sBZIP2_BINARY=bz2 \  
    -sBZIP2_INCLUDE=${MINGW_PREFIX}/include \  
    -sBZIP2_LIBPATH=${MINGW_PREFIX}/lib \  
    -sNO_ZLIB \  
    -sZLIB_BINARY=z \  
    -sZLIB_INCLUDE=${MINGW_PREFIX}/include \  
    -sZLIB_LIBPATH=${MINGW_PREFIX}/lib \  
    -d2 \  
    -q  
```  
  
And this is the build output. As you can see, b2 reports that the build is complete, but boost_fiber was not built:  
  
```  
notice: Searching '.' for project-config configuration file 'project-config.jam'.  
notice: Loading project-config configuration file 'project-config.jam' from '.'.  
notice: will use 'g++' for gcc, condition <toolset>gcc-11  
notice: using gcc libraries :: <toolset>gcc-11 :: C:\apps\msys64\mingw64/bin C:\apps\msys64\mingw64/lib C:\apps\msys64\mingw64/lib32 C:\apps\msys64\mingw64/lib64  
notice: using gcc archiver :: <toolset>gcc-11 :: C:/apps/msys64/mingw64/x86_64-w64-mingw32/bin/ar.exe  
notice: using rc compiler :: <toolset>gcc-11 :: C:\apps\msys64\mingw64\bin\windres.exe  
notice: [zlib] Using pre-installed library  
notice: [zlib] Condition  
notice: [bzip2] Using pre-installed library  
notice: [bzip2] Condition  
notice: [lzma] Using pre-installed library  
notice: [lzma] Condition  
notice: [zstd] Using pre-installed library  
notice: [zstd] Condition  
Performing configuration checks  
  
    - default address-model    : 64-bit [1]  
    - default architecture     : x86 [1]  
  
Building the Boost C++ Libraries.  
  
  
    - has std::atomic_ref      : no [2]  
    - has statx                : no [2]  
    - has statx syscall        : no [2]  
    - has BCrypt API           : yes [2]  
    - has init_priority attribute : yes [2]  
    - has stat::st_blksize     : no [2]  
    - has stat::st_mtim        : no [2]  
    - has stat::st_mtimensec   : no [2]  
    - has stat::st_mtimespec   : no [2]  
    - has stat::st_birthtim    : no [2]  
    - has stat::st_birthtimensec : no [2]  
    - has stat::st_birthtimespec : no [2]  
    - compiler supports SSE2   : yes [2]  
    - compiler supports SSE4.1 : yes [2]  
    - has synchronization.lib  : yes [2]  
    - cxx11_auto_declarations  : yes [2]  
    - cxx11_constexpr          : yes [2]  
    - cxx11_defaulted_functions : yes [2]  
    - cxx11_final              : yes [2]  
    - cxx11_hdr_mutex          : yes [2]  
    - cxx11_hdr_tuple          : yes [2]  
    - cxx11_lambdas            : yes [2]  
    - cxx11_noexcept           : yes [2]  
    - cxx11_nullptr            : yes [2]  
    - cxx11_rvalue_references  : yes [2]  
    - cxx11_template_aliases   : yes [2]  
    - cxx11_thread_local       : yes [2]  
    - cxx11_variadic_templates : yes [2]  
    - cxx11_auto_declarations  : yes [3]  
    - cxx11_constexpr          : yes [3]  
    - cxx11_defaulted_functions : yes [3]  
    - cxx11_final              : yes [3]  
    - cxx11_hdr_mutex          : yes [3]  
    - cxx11_hdr_tuple          : yes [3]  
    - cxx11_lambdas            : yes [3]  
    - cxx11_noexcept           : yes [3]  
    - cxx11_nullptr            : yes [3]  
    - cxx11_rvalue_references  : yes [3]  
    - cxx11_template_aliases   : yes [3]  
    - cxx11_thread_local       : yes [3]  
    - cxx11_variadic_templates : yes [3]  
    - has std::atomic_ref      : no [4]  
    - has statx                : no [4]  
    - has statx syscall        : no [4]  
    - has BCrypt API           : yes [4]  
    - has init_priority attribute : yes [4]  
    - has stat::st_blksize     : no [4]  
    - has stat::st_mtim        : no [4]  
    - has stat::st_mtimensec   : no [4]  
    - has stat::st_mtimespec   : no [4]  
    - has stat::st_birthtim    : no [4]  
    - has stat::st_birthtimensec : no [4]  
    - has stat::st_birthtimespec : no [4]  
    - compiler supports SSE2   : yes [4]  
    - compiler supports SSE4.1 : yes [4]  
    - has synchronization.lib  : yes [4]  
    - cxx11_auto_declarations  : yes [4]  
    - cxx11_constexpr          : yes [4]  
    - cxx11_defaulted_functions : yes [4]  
    - cxx11_final              : yes [4]  
    - cxx11_hdr_mutex          : yes [4]  
    - cxx11_hdr_tuple          : yes [4]  
    - cxx11_lambdas            : yes [4]  
    - cxx11_noexcept           : yes [4]  
    - cxx11_nullptr            : yes [4]  
    - cxx11_rvalue_references  : yes [4]  
    - cxx11_template_aliases   : yes [4]  
    - cxx11_thread_local       : yes [4]  
    - cxx11_variadic_templates : yes [4]  
    - cxx11_auto_declarations  : yes [5]  
    - cxx11_constexpr          : yes [5]  
    - cxx11_defaulted_functions : yes [5]  
    - cxx11_final              : yes [5]  
    - cxx11_hdr_mutex          : yes [5]  
    - cxx11_hdr_tuple          : yes [5]  
    - cxx11_lambdas            : yes [5]  
    - cxx11_noexcept           : yes [5]  
    - cxx11_nullptr            : yes [5]  
    - cxx11_rvalue_references  : yes [5]  
    - cxx11_template_aliases   : yes [5]  
    - cxx11_thread_local       : yes [5]  
    - cxx11_variadic_templates : yes [5]  
  
[1] gcc-11  
[2] gcc-11/release/pch-off/python-3.9/threadapi-win32/threading-multi/visibility-hidden  
[3] gcc-11/release/build-no/pch-off/python-3.9/threadapi-win32/threading-multi/visibility-hidden  
[4] gcc-11/release/link-static/pch-off/python-3.9/threadapi-win32/threading-multi/visibility-hidden  
[5] gcc-11/release/build-no/link-static/pch-off/python-3.9/threadapi-win32/threading-multi/visibility-hidden  
  
Component configuration:  
  
    - atomic                   : not building  
    - chrono                   : not building  
    - container                : not building  
    - context                  : not building  
    - contract                 : not building  
    - coroutine                : not building  
    - date_time                : not building  
    - exception                : not building  
    - fiber                    : building  
    - filesystem               : not building  
    - graph                    : not building  
    - graph_parallel           : not building  
    - headers                  : not building  
    - iostreams                : not building  
    - json                     : not building  
    - locale                   : not building  
    - log                      : not building  
    - math                     : not building  
    - mpi                      : not building  
    - nowide                   : not building  
    - program_options          : not building  
    - python                   : not building  
    - random                   : not building  
    - regex                    : not building  
    - serialization            : not building  
    - stacktrace               : not building  
    - system                   : not building  
    - test                     : not building  
    - thread                   : not building  
    - timer                    : not building  
    - type_erasure             : not building  
    - wave                     : not building  
  
...found 2 targets...  
  
  
The Boost C++ Libraries were successfully built!  
  
The following directory should be added to compiler include paths:  
  
    D:\dev\other\MINGW-packages\mingw-w64-boost\src\build-x86_64-w64-mingw32  
  
The following directory should be added to linker library paths:  
  
    D:\dev\other\MINGW-packages\mingw-w64-boost\src\build-x86_64-w64-mingw32\stage\lib  
```

---

## Comment 1

> Username: oscarfv  
> Created at: 2021-12-12 11:52:54 UTC  
> Url: https://github.com/boostorg/build/issues/749#issuecomment-991884035  

It turns out that removing `--no-cmake-config` from the command line fixes the build.

---

## Comment 2

> Username: oscarfv  
> Created at: 2021-12-12 12:28:54 UTC  
> Updated at: 2021-12-12 12:30:10 UTC  
> Url: https://github.com/boostorg/build/issues/749#issuecomment-991889712  

Removing `--no-cmake-config` does not totally resolve the problem: for `fiber` and `stacktrace` the libraries are built, but not copied to `/stage`. Tested with:  
  
` b2 address-model=64 link=shared,static pch=off runtime-link=shared threading=multi threadapi=win32 toolset=gcc variant=release --layout=tagged -d2 stage`  
  
They are not installed either after `b2 ... install`.

---

## Comment 3

> Username: github-actions[bot]  
> Created at: 2021-12-12 15:05:56 UTC  
> Url: https://github.com/boostorg/build/issues/749#issuecomment-991914365  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
