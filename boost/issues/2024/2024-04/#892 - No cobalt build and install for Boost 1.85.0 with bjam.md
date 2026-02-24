# #892 - No cobalt build and install for Boost 1.85.0 with bjam [Closed]

> Username: liaoya  
> Created at: 2024-04-26 02:04:35 UTC  
> Updated at: 2024-04-26 02:05:54 UTC  
> Closed at: 2024-04-26 02:04:53 UTC  
> Url: https://github.com/boostorg/boost/issues/892  

I download `boost_1_85_0.tar.bz2` and built it on Ubuntu `22.04` with gcc `11.4.0`. I run `./b2 link=static threading=multi; ls stage/lib`. The following is the output  
  
```text  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
  
Building the Boost C++ Libraries.  
  
  
    - compiler supports SSE2   : yes (cached) [2]  
    - compiler supports SSE4.1 : yes (cached) [2]  
    - cxx11_static_assert      : yes (cached) [2]  
    - cxx11_variadic_templates : yes (cached) [2]  
    - cxx11_decltype           : yes (cached) [2]  
    - GCC libquadmath and __float128 support : yes (cached) [2]  
    - cxx11_hdr_ratio          : yes (cached) [2]  
    - cxx11_template_aliases   : yes (cached) [2]  
    - cxx11_char16_t           : yes (cached) [2]  
    - cxx11_char32_t           : yes (cached) [2]  
    - cxx20_hdr_concepts       : no  (cached) [2]  
    - cxx20_hdr_concepts       : no  (cached) [3]  
    - has std::atomic_ref      : no  (cached) [2]  
    - has statx                : yes (cached) [2]  
    - cxx11_rvalue_references  : yes (cached) [2]  
    - cxx11_scoped_enums       : yes (cached) [2]  
    - cxx11_noexcept           : yes (cached) [2]  
    - cxx11_nullptr            : yes (cached) [2]  
    - cxx11_defaulted_functions : yes (cached) [2]  
    - cxx11_defaulted_moves    : yes (cached) [2]  
    - cxx11_deleted_functions  : yes (cached) [2]  
    - cxx11_function_template_default_args : yes (cached) [2]  
    - cxx11_final              : yes (cached) [2]  
    - cxx11_override           : yes (cached) [2]  
    - has init_priority attribute : yes (cached) [2]  
    - has stat::st_blksize     : yes (cached) [2]  
    - has stat::st_mtim        : yes (cached) [2]  
    - has stat::st_mtimensec   : no  (cached) [2]  
    - has stat::st_mtimespec   : no  (cached) [2]  
    - has stat::st_birthtim    : no  (cached) [2]  
    - has stat::st_birthtimensec : no  (cached) [2]  
    - has stat::st_birthtimespec : no  (cached) [2]  
    - has fdopendir(O_NOFOLLOW) : yes (cached) [2]  
    - has dirent::d_type       : yes (cached) [2]  
    - has POSIX *at APIs       : yes (cached) [2]  
    - has fallocate            : yes (cached) [2]  
    - cxx11_auto_declarations  : yes (cached) [2]  
    - cxx11_constexpr          : yes (cached) [2]  
    - cxx11_hdr_mutex          : yes (cached) [2]  
    - cxx11_hdr_tuple          : yes (cached) [2]  
    - cxx11_lambdas            : yes (cached) [2]  
    - cxx11_thread_local       : yes (cached) [2]  
    - has_icu builds           : yes (cached) [2]  
warning: Graph library does not contain MPI-based parallel components.  
note: to enable them, add "using mpi ;" to your user-config.jam.  
note: to suppress this message, pass "--without-graph_parallel" to bjam.  
    - zlib                     : yes (cached) [4]  
    - bzip2                    : yes (cached) [4]  
    - lzma                     : yes (cached) [4]  
    - zstd                     : yes (cached) [4]  
    - has_lzma_cputhreads builds : yes (cached) [2]  
    - cxx11_basic_alignas      : yes (cached) [2]  
    - icu                      : yes (cached) [2]  
    - iconv (libc)             : yes (cached) [2]  
    - cxx11_hdr_functional     : yes (cached) [2]  
    - cxx11_hdr_type_traits    : yes (cached) [2]  
    - cxx11_range_based_for    : yes (cached) [2]  
    - cxx11_smart_ptr          : yes (cached) [2]  
    - lockfree boost::atomic_flag : yes (cached) [2]  
    - native atomic int32 supported : yes (cached) [2]  
    - native syslog supported  : yes (cached) [2]  
    - pthread supports robust mutexes : yes (cached) [2]  
    - Boost.Regex is header-only : yes (cached) [2]  
    - compiler supports SSSE3  : yes (cached) [2]  
    - compiler supports AVX2   : yes (cached) [2]  
    - gcc visibility           : yes (cached) [2]  
    - sfinae_expr              : yes (cached) [2]  
    - cxx11_unified_initialization_syntax : yes (cached) [2]  
    - cxx11_hdr_initializer_list : yes (cached) [2]  
    - cxx11_hdr_chrono         : yes (cached) [2]  
    - cxx11_numeric_limits     : yes (cached) [2]  
    - cxx11_hdr_array          : yes (cached) [2]  
    - cxx11_hdr_atomic         : yes (cached) [2]  
    - cxx11_allocator          : yes (cached) [2]  
    - cxx11_explicit_conversion_operators : yes (cached) [2]  
    - cxx14_decltype_auto      : yes (cached) [2]  
    - cxx14_generic_lambdas    : yes (cached) [2]  
    - cxx14_return_type_deduction : yes (cached) [2]  
    - cxx14_variable_templates : yes (cached) [2]  
    - long double support      : yes (cached) [2]  
warning: skipping optional Message Passing Interface (MPI) library.  
note: to enable MPI support, add "using mpi ;" to user-config.jam.  
note: to suppress this message, pass "--without-mpi" to bjam.  
note: otherwise, you can safely ignore this message.  
    - Has Large File Support   : yes (cached) [2]  
    - Has attribute init_priority : yes (cached) [2]  
warning: No python installation configured and autoconfiguration  
note: failed.  See http://www.boost.org/libs/python/doc/building.html  
note: for configuration instructions or pass --without-python to  
note: suppress this message and silently skip all Boost.Python targets  
    - std_wstreambuf builds    : yes (cached) [2]  
    - std_wstreambuf           : yes (cached) [2]  
    - libbacktrace builds      : yes (cached) [2]  
    - addr2line builds         : yes (cached) [2]  
    - WinDbg builds            : no  (cached) [2]  
    - cxx11_rvalue_references  : yes (cached) [3]  
    - WinDbg builds            : no  (cached) [3]  
    - WinDbgCached builds      : no  (cached) [2]  
    - WinDbgCached builds      : no  (cached) [3]  
    - BOOST_COMP_GNUC >= 4.3.0 : yes (cached) [2]  
    - cxx11_hdr_thread         : yes (cached) [2]  
    - cxx11_hdr_regex          : yes (cached) [2]  
  
[1] gcc-11  
[2] gcc-11/release/link-static/threading-multi/visibility-hidden  
[3] gcc-11/release/build-no/link-static/threading-multi/visibility-hidden  
[4] link-static  
  
Component configuration:  
  
    - atomic                   : building  
    - charconv                 : building  
    - chrono                   : building  
    - cobalt                   : building  
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
  
  
  
The Boost C++ Libraries were successfully built!  
  
The following directory should be added to compiler include paths:  
  
    /home/tshen/Downloads/boost  
  
The following directory should be added to linker library paths:  
  
    /home/tshen/Downloads/boost/stage/lib  
  
cmake                 libboost_date_time.a   libboost_log.a        libboost_nowide.a                libboost_stacktrace_basic.a           libboost_unit_test_framework.a  
libboost_atomic.a     libboost_exception.a   libboost_log_setup.a  libboost_prg_exec_monitor.a      libboost_stacktrace_from_exception.a  libboost_url.a  
libboost_charconv.a   libboost_fiber.a       libboost_math_c99.a   libboost_program_options.a       libboost_stacktrace_noop.a            libboost_wave.a  
libboost_chrono.a     libboost_filesystem.a  libboost_math_c99f.a  libboost_random.a                libboost_system.a                     libboost_wserialization.a  
libboost_container.a  libboost_graph.a       libboost_math_c99l.a  libboost_regex.a                 libboost_test_exec_monitor.a  
libboost_context.a    libboost_iostreams.a   libboost_math_tr1.a   libboost_serialization.a         libboost_thread.a  
libboost_contract.a   libboost_json.a        libboost_math_tr1f.a  libboost_stacktrace_addr2line.a  libboost_timer.a  
libboost_coroutine.a  libboost_locale.a      libboost_math_tr1l.a  libboost_stacktrace_backtrace.a  libboost_type_erasure.a  
```  
  
I cannot find `libboost_cobalt.a` under `stage/lib`. I have to enter `cobalt` directory and run `cmake -H. -Bbuild; sudo cmake --build build --target install/strip` do the build manually. This time I find `libboost_cobalt.a` under `/usr/local/lib`.  
  
Then I can run `g++ --std=c++20 delay.cpp -lboost_cobalt` to build the demo code.

---

## Comment 1

> Username: liaoya  
> Created at: 2024-04-26 02:05:53 UTC  
> Url: https://github.com/boostorg/boost/issues/892#issuecomment-2078498678  

I found the same issue have been moved [No cobalt build and install for Boost 1.85.0 with bjam](https://github.com/boostorg/cobalt/issues/177)
