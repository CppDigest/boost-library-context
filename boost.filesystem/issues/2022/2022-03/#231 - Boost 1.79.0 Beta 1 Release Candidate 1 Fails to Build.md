# #231 - Boost 1.79.0 Beta 1 Release Candidate 1 Fails to Build [Closed]

> Username: mborland  
> Created at: 2022-03-11 10:55:59 UTC  
> Updated at: 2022-03-11 11:07:54 UTC  
> Closed at: 2022-03-11 11:07:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/231  

The RC fails to build using c++11/14/17/20 with Apple Clang on M1 Pro Architecture. Below is a complete copy of the log and configuration details.  
  
```  
╰─ ./b2 cxxstd=17                                                                                                                                                                                                ─╯  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : arm (cached) [1]  
  
Building the Boost C++ Libraries.  
  
  
    - has std::atomic_ref      : no  (cached) [2]  
    - has statx                : no  (cached) [2]  
    - has statx syscall        : no  (cached) [2]  
    - has init_priority attribute : yes (cached) [2]  
    - has stat::st_blksize     : no  (cached) [2]  
    - has stat::st_mtim        : no  (cached) [2]  
    - has stat::st_mtimensec   : no  (cached) [2]  
    - has stat::st_mtimespec   : yes (cached) [2]  
    - has stat::st_birthtim    : no  (cached) [2]  
    - has stat::st_birthtimensec : no  (cached) [2]  
    - has stat::st_birthtimespec : yes (cached) [2]  
    - has fdopendir(O_NOFOLLOW) : yes (cached) [2]  
    - cxx11_auto_declarations  : yes (cached) [2]  
    - cxx11_constexpr          : yes (cached) [2]  
    - cxx11_defaulted_functions : yes (cached) [2]  
    - cxx11_final              : yes (cached) [2]  
    - cxx11_hdr_mutex          : yes (cached) [2]  
    - cxx11_hdr_tuple          : yes (cached) [2]  
    - cxx11_lambdas            : yes (cached) [2]  
    - cxx11_noexcept           : yes (cached) [2]  
    - cxx11_nullptr            : yes (cached) [2]  
    - cxx11_rvalue_references  : yes (cached) [2]  
    - cxx11_template_aliases   : yes (cached) [2]  
    - cxx11_thread_local       : yes (cached) [2]  
    - cxx11_variadic_templates : yes (cached) [2]  
    - cxx11_auto_declarations  : yes (cached) [3]  
    - cxx11_constexpr          : yes (cached) [3]  
    - cxx11_defaulted_functions : yes (cached) [3]  
    - cxx11_final              : yes (cached) [3]  
    - cxx11_hdr_mutex          : yes (cached) [3]  
    - cxx11_hdr_tuple          : yes (cached) [3]  
    - cxx11_lambdas            : yes (cached) [3]  
    - cxx11_noexcept           : yes (cached) [3]  
    - cxx11_nullptr            : yes (cached) [3]  
    - cxx11_rvalue_references  : yes (cached) [3]  
    - cxx11_template_aliases   : yes (cached) [3]  
    - cxx11_thread_local       : yes (cached) [3]  
    - cxx11_variadic_templates : yes (cached) [3]  
    - has_icu builds           : no  (cached) [2]  
warning: Graph library does not contain MPI-based parallel components.  
note: to enable them, add "using mpi ;" to your user-config.jam.  
note: to suppress this message, pass "--without-graph_parallel" to bjam.  
    - zlib                     : yes (cached)  
    - bzip2                    : yes (cached)  
    - lzma                     : no  (cached)  
    - zstd                     : yes (cached)  
    - has_lzma_cputhreads builds : no  (cached) [2]  
    - cxx11_decltype           : yes (cached) [2]  
    - cxx11_basic_alignas      : yes (cached) [2]  
    - iconv (libc)             : no  (cached) [2]  
    - iconv (separate)         : yes (cached) [2]  
    - icu                      : no  (cached) [2]  
    - icu (lib64)              : no  (cached) [2]  
    - native atomic int32 supported : yes (cached) [2]  
    - native syslog supported  : yes (cached) [2]  
    - pthread supports robust mutexes : no  (cached) [2]  
    - lockfree boost::atomic_flag : yes (cached) [2]  
    - gcc visibility           : yes (cached) [2]  
    - sfinae_expr              : yes (cached) [2]  
    - cxx11_unified_initialization_syntax : yes (cached) [2]  
    - cxx11_hdr_initializer_list : yes (cached) [2]  
    - cxx11_hdr_chrono         : yes (cached) [2]  
    - cxx11_numeric_limits     : yes (cached) [2]  
    - cxx11_hdr_array          : yes (cached) [2]  
    - cxx11_hdr_atomic         : yes (cached) [2]  
    - cxx11_hdr_type_traits    : yes (cached) [2]  
    - cxx11_allocator          : yes (cached) [2]  
    - cxx11_explicit_conversion_operators : yes (cached) [2]  
    - long double support      : yes (cached) [2]  
warning: skipping optional Message Passing Interface (MPI) library.  
note: to enable MPI support, add "using mpi ;" to user-config.jam.  
note: to suppress this message, pass "--without-mpi" to bjam.  
note: otherwise, you can safely ignore this message.  
    - cxx11_static_assert      : yes (cached) [2]  
    - std::fstream is moveable and swappable : yes (cached) [2]  
    - Has Large File Support   : yes (cached) [2]  
    - Has attribute init_priority : yes (cached) [2]  
    - libbacktrace builds      : no  (cached) [2]  
    - libbacktrace builds      : no  (cached) [3]  
    - addr2line builds         : yes (cached) [2]  
    - WinDbg builds            : no  (cached) [2]  
    - WinDbg builds            : no  (cached) [3]  
    - WinDbgCached builds      : no  (cached) [2]  
    - WinDbgCached builds      : no  (cached) [3]  
    - BOOST_COMP_GNUC >= 4.3.0 : no  (cached) [2]  
    - BOOST_COMP_GNUC >= 4.3.0 : no  (cached) [4]  
    - cxx11_hdr_thread         : yes (cached) [2]  
    - cxx11_hdr_regex          : yes (cached) [2]  
    - has std::atomic_ref      : no  (cached) [4]  
    - has statx                : no  (cached) [4]  
    - has statx syscall        : no  (cached) [4]  
    - has init_priority attribute : yes (cached) [4]  
    - has stat::st_blksize     : no  (cached) [4]  
    - has stat::st_mtim        : no  (cached) [4]  
    - has stat::st_mtimensec   : no  (cached) [4]  
    - has stat::st_mtimespec   : yes (cached) [4]  
    - has stat::st_birthtim    : no  (cached) [4]  
    - has stat::st_birthtimensec : no  (cached) [4]  
    - has stat::st_birthtimespec : yes (cached) [4]  
    - has fdopendir(O_NOFOLLOW) : yes (cached) [4]  
    - cxx11_auto_declarations  : yes (cached) [4]  
    - cxx11_constexpr          : yes (cached) [4]  
    - cxx11_defaulted_functions : yes (cached) [4]  
    - cxx11_final              : yes (cached) [4]  
    - cxx11_hdr_mutex          : yes (cached) [4]  
    - cxx11_hdr_tuple          : yes (cached) [4]  
    - cxx11_lambdas            : yes (cached) [4]  
    - cxx11_noexcept           : yes (cached) [4]  
    - cxx11_nullptr            : yes (cached) [4]  
    - cxx11_rvalue_references  : yes (cached) [4]  
    - cxx11_template_aliases   : yes (cached) [4]  
    - cxx11_thread_local       : yes (cached) [4]  
    - cxx11_variadic_templates : yes (cached) [4]  
    - cxx11_auto_declarations  : yes (cached) [5]  
    - cxx11_constexpr          : yes (cached) [5]  
    - cxx11_defaulted_functions : yes (cached) [5]  
    - cxx11_final              : yes (cached) [5]  
    - cxx11_hdr_mutex          : yes (cached) [5]  
    - cxx11_hdr_tuple          : yes (cached) [5]  
    - cxx11_lambdas            : yes (cached) [5]  
    - cxx11_noexcept           : yes (cached) [5]  
    - cxx11_nullptr            : yes (cached) [5]  
    - cxx11_rvalue_references  : yes (cached) [5]  
    - cxx11_template_aliases   : yes (cached) [5]  
    - cxx11_thread_local       : yes (cached) [5]  
    - cxx11_variadic_templates : yes (cached) [5]  
    - has_icu builds           : no  (cached) [4]  
    - zlib                     : yes (cached) [6]  
    - bzip2                    : yes (cached) [6]  
    - lzma                     : no  (cached) [6]  
    - zstd                     : yes (cached) [6]  
    - has_lzma_cputhreads builds : no  (cached) [4]  
    - cxx11_decltype           : yes (cached) [4]  
    - cxx11_basic_alignas      : yes (cached) [4]  
    - iconv (libc)             : no  (cached) [4]  
    - iconv (separate)         : yes (cached) [4]  
    - icu                      : no  (cached) [4]  
    - icu (lib64)              : no  (cached) [4]  
    - native atomic int32 supported : yes (cached) [4]  
    - native syslog supported  : yes (cached) [4]  
    - pthread supports robust mutexes : no  (cached) [4]  
    - lockfree boost::atomic_flag : yes (cached) [4]  
    - gcc visibility           : yes (cached) [4]  
    - sfinae_expr              : yes (cached) [4]  
    - cxx11_unified_initialization_syntax : yes (cached) [4]  
    - cxx11_hdr_initializer_list : yes (cached) [4]  
    - cxx11_hdr_chrono         : yes (cached) [4]  
    - cxx11_numeric_limits     : yes (cached) [4]  
    - cxx11_hdr_array          : yes (cached) [4]  
    - cxx11_hdr_atomic         : yes (cached) [4]  
    - cxx11_hdr_type_traits    : yes (cached) [4]  
    - cxx11_allocator          : yes (cached) [4]  
    - cxx11_explicit_conversion_operators : yes (cached) [4]  
    - long double support      : yes (cached) [4]  
    - cxx11_static_assert      : yes (cached) [4]  
    - std::fstream is moveable and swappable : yes (cached) [4]  
    - Has Large File Support   : yes (cached) [4]  
    - Has attribute init_priority : yes (cached) [4]  
    - libbacktrace builds      : no  (cached) [4]  
    - libbacktrace builds      : no  (cached) [5]  
    - addr2line builds         : yes (cached) [4]  
    - WinDbg builds            : no  (cached) [4]  
    - WinDbg builds            : no  (cached) [5]  
    - WinDbgCached builds      : no  (cached) [4]  
    - WinDbgCached builds      : no  (cached) [5]  
    - cxx11_hdr_thread         : yes (cached) [4]  
    - cxx11_hdr_regex          : yes (cached) [4]  
  
[1] clang-13  
[2] clang-darwin-13/release/cxxstd-17-iso/python-2.7/threadapi-pthread/threading-multi/visibility-hidden  
[3] clang-darwin-13/release/build-no/cxxstd-17-iso/python-2.7/threadapi-pthread/threading-multi/visibility-hidden  
[4] clang-darwin-13/release/cxxstd-17-iso/link-static/python-2.7/threadapi-pthread/threading-multi/visibility-hidden  
[5] clang-darwin-13/release/build-no/cxxstd-17-iso/link-static/python-2.7/threadapi-pthread/threading-multi/visibility-hidden  
[6] link-static  
  
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
    - wave                     : building  
  
...patience...  
...patience...  
...patience...  
...patience...  
...patience...  
...patience...  
...found 17418 targets...  
...updating 39 targets...  
clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden/codecvt_error_category.o  
libs/filesystem/src/codecvt_error_category.cpp:73:60: error: exception specification in declaration does not match previous declaration  
BOOST_FILESYSTEM_DECL boost::system::error_category const& codecvt_error_category() BOOST_NOEXCEPT  
                                                           ^  
/usr/local/include/boost/filesystem/path_traits.hpp:32:53: note: previous declaration is here  
BOOST_FILESYSTEM_DECL const system::error_category& codecvt_error_category();  
                                                    ^  
1 error generated.  
  
    "clang++" -x c++ -std=c++17 -fvisibility-inlines-hidden -fPIC -O3 -Wall -fvisibility=hidden -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW -DBOOST_FILESYSTEM_HAS_INIT_PRIORITY -DBOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC -DBOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC -DBOOST_FILESYSTEM_NO_CXX20_ATOMIC_REF -DBOOST_FILESYSTEM_SOURCE -DNDEBUG -I"." -I"libs/filesystem/src"  -c -o "bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden/codecvt_error_category.o" "libs/filesystem/src/codecvt_error_category.cpp"  
  
...failed clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden/codecvt_error_category.o...  
clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/codecvt_error_category.o  
libs/filesystem/src/codecvt_error_category.cpp:73:60: error: exception specification in declaration does not match previous declaration  
BOOST_FILESYSTEM_DECL boost::system::error_category const& codecvt_error_category() BOOST_NOEXCEPT  
                                                           ^  
/usr/local/include/boost/filesystem/path_traits.hpp:32:53: note: previous declaration is here  
BOOST_FILESYSTEM_DECL const system::error_category& codecvt_error_category();  
                                                    ^  
1 error generated.  
  
    "clang++" -x c++ -std=c++17 -fvisibility-inlines-hidden -O3 -Wall -fvisibility=hidden -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW -DBOOST_FILESYSTEM_HAS_INIT_PRIORITY -DBOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC -DBOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC -DBOOST_FILESYSTEM_NO_CXX20_ATOMIC_REF -DBOOST_FILESYSTEM_SOURCE -DBOOST_FILESYSTEM_STATIC_LINK=1 -DNDEBUG -I"." -I"libs/filesystem/src"  -c -o "bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/codecvt_error_category.o" "libs/filesystem/src/codecvt_error_category.cpp"  
  
...failed clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/codecvt_error_category.o...  
clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden/directory.o  
libs/filesystem/src/directory.cpp:177:42: error: out-of-line definition of 'operator new' does not match any declaration in 'boost::filesystem::detail::dir_itr_imp'  
BOOST_FILESYSTEM_DECL void* dir_itr_imp::operator new(std::size_t class_size, std::size_t extra_size) BOOST_NOEXCEPT  
                                         ^~~~~~~~  
libs/filesystem/src/directory.cpp:190:41: error: out-of-line definition of 'operator delete' does not match any declaration in 'boost::filesystem::detail::dir_itr_imp'  
BOOST_FILESYSTEM_DECL void dir_itr_imp::operator delete(void* p, std::size_t extra_size) BOOST_NOEXCEPT  
                                        ^~~~~~~~  
libs/filesystem/src/directory.cpp:195:41: error: out-of-line definition of 'operator delete' does not match any declaration in 'boost::filesystem::detail::dir_itr_imp'  
BOOST_FILESYSTEM_DECL void dir_itr_imp::operator delete(void* p) BOOST_NOEXCEPT  
                                        ^~~~~~~~  
libs/filesystem/src/directory.cpp:409:64: error: no member named '_detail_no_follow' in 'boost::filesystem::directory_options'; did you mean '_detail_no_push'?  
    if ((opts & static_cast< unsigned int >(directory_options::_detail_no_follow)) != 0u)  
                                            ~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~  
                                                               _detail_no_push  
/usr/local/include/boost/filesystem/directory.hpp:250:5: note: '_detail_no_push' declared here  
    _detail_no_push = 1u << 4           // internal use only  
    ^  
libs/filesystem/src/directory.cpp:1035:1: warning: attribute declaration must precede definition [-Wignored-attributes]  
BOOST_FILESYSTEM_DECL  
^  
/usr/local/include/boost/filesystem/config.hpp:97:31: note: expanded from macro 'BOOST_FILESYSTEM_DECL'  
#define BOOST_FILESYSTEM_DECL BOOST_SYMBOL_EXPORT  
                              ^  
/usr/local/include/boost/config/compiler/clang.hpp:114:46: note: expanded from macro 'BOOST_SYMBOL_EXPORT'  
#  define BOOST_SYMBOL_EXPORT __attribute__((__visibility__("default")))  
                                             ^  
/usr/local/include/boost/filesystem/directory.hpp:286:5: note: previous definition is here  
    ~dir_itr_imp() BOOST_NOEXCEPT  
    ^  
libs/filesystem/src/directory.cpp:1036:14: error: redefinition of '~dir_itr_imp'  
dir_itr_imp::~dir_itr_imp() BOOST_NOEXCEPT  
             ^  
/usr/local/include/boost/filesystem/directory.hpp:286:5: note: previous definition is here  
    ~dir_itr_imp() BOOST_NOEXCEPT  
    ^  
1 warning and 5 errors generated.  
  
    "clang++" -x c++ -std=c++17 -fvisibility-inlines-hidden -fPIC -O3 -Wall -fvisibility=hidden -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW -DBOOST_FILESYSTEM_HAS_INIT_PRIORITY -DBOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC -DBOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC -DBOOST_FILESYSTEM_NO_CXX20_ATOMIC_REF -DBOOST_FILESYSTEM_SOURCE -DNDEBUG -I"." -I"libs/filesystem/src"  -c -o "bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden/directory.o" "libs/filesystem/src/directory.cpp"  
  
...failed clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden/directory.o...  
clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/directory.o  
libs/filesystem/src/directory.cpp:177:42: error: out-of-line definition of 'operator new' does not match any declaration in 'boost::filesystem::detail::dir_itr_imp'  
BOOST_FILESYSTEM_DECL void* dir_itr_imp::operator new(std::size_t class_size, std::size_t extra_size) BOOST_NOEXCEPT  
                                         ^~~~~~~~  
libs/filesystem/src/directory.cpp:190:41: error: out-of-line definition of 'operator delete' does not match any declaration in 'boost::filesystem::detail::dir_itr_imp'  
BOOST_FILESYSTEM_DECL void dir_itr_imp::operator delete(void* p, std::size_t extra_size) BOOST_NOEXCEPT  
                                        ^~~~~~~~  
libs/filesystem/src/directory.cpp:195:41: error: out-of-line definition of 'operator delete' does not match any declaration in 'boost::filesystem::detail::dir_itr_imp'  
BOOST_FILESYSTEM_DECL void dir_itr_imp::operator delete(void* p) BOOST_NOEXCEPT  
                                        ^~~~~~~~  
libs/filesystem/src/directory.cpp:409:64: error: no member named '_detail_no_follow' in 'boost::filesystem::directory_options'; did you mean '_detail_no_push'?  
    if ((opts & static_cast< unsigned int >(directory_options::_detail_no_follow)) != 0u)  
                                            ~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~  
                                                               _detail_no_push  
/usr/local/include/boost/filesystem/directory.hpp:250:5: note: '_detail_no_push' declared here  
    _detail_no_push = 1u << 4           // internal use only  
    ^  
libs/filesystem/src/directory.cpp:1036:14: error: redefinition of '~dir_itr_imp'  
dir_itr_imp::~dir_itr_imp() BOOST_NOEXCEPT  
             ^  
/usr/local/include/boost/filesystem/directory.hpp:286:5: note: previous definition is here  
    ~dir_itr_imp() BOOST_NOEXCEPT  
    ^  
5 errors generated.  
  
    "clang++" -x c++ -std=c++17 -fvisibility-inlines-hidden -O3 -Wall -fvisibility=hidden -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW -DBOOST_FILESYSTEM_HAS_INIT_PRIORITY -DBOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC -DBOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC -DBOOST_FILESYSTEM_NO_CXX20_ATOMIC_REF -DBOOST_FILESYSTEM_SOURCE -DBOOST_FILESYSTEM_STATIC_LINK=1 -DNDEBUG -I"." -I"libs/filesystem/src"  -c -o "bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/directory.o" "libs/filesystem/src/directory.cpp"  
  
...failed clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/directory.o...  
clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden/operations.o  
libs/filesystem/src/operations.cpp:904:109: error: no member named '_detail_no_follow' in 'boost::filesystem::directory_options'; did you mean '_detail_no_push'?  
            fs::detail::directory_iterator_construct(itr, p, static_cast< unsigned int >(directory_options::_detail_no_follow), &dit_create_ec);  
                                                                                         ~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~  
                                                                                                            _detail_no_push  
/usr/local/include/boost/filesystem/directory.hpp:250:5: note: '_detail_no_push' declared here  
    _detail_no_push = 1u << 4           // internal use only  
    ^  
1 error generated.  
  
    "clang++" -x c++ -std=c++17 -fvisibility-inlines-hidden -fPIC -O3 -Wall -fvisibility=hidden -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW -DBOOST_FILESYSTEM_HAS_INIT_PRIORITY -DBOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC -DBOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC -DBOOST_FILESYSTEM_NO_CXX20_ATOMIC_REF -DBOOST_FILESYSTEM_SOURCE -DNDEBUG -I"." -I"libs/filesystem/src"  -c -o "bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden/operations.o" "libs/filesystem/src/operations.cpp"  
  
...failed clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden/operations.o...  
clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/operations.o  
libs/filesystem/src/operations.cpp:904:109: error: no member named '_detail_no_follow' in 'boost::filesystem::directory_options'; did you mean '_detail_no_push'?  
            fs::detail::directory_iterator_construct(itr, p, static_cast< unsigned int >(directory_options::_detail_no_follow), &dit_create_ec);  
                                                                                         ~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~  
                                                                                                            _detail_no_push  
/usr/local/include/boost/filesystem/directory.hpp:250:5: note: '_detail_no_push' declared here  
    _detail_no_push = 1u << 4           // internal use only  
    ^  
1 error generated.  
  
    "clang++" -x c++ -std=c++17 -fvisibility-inlines-hidden -O3 -Wall -fvisibility=hidden -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW -DBOOST_FILESYSTEM_HAS_INIT_PRIORITY -DBOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC -DBOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC -DBOOST_FILESYSTEM_NO_CXX20_ATOMIC_REF -DBOOST_FILESYSTEM_SOURCE -DBOOST_FILESYSTEM_STATIC_LINK=1 -DNDEBUG -I"." -I"libs/filesystem/src"  -c -o "bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/operations.o" "libs/filesystem/src/operations.cpp"  
  
...failed clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/operations.o...  
clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/path.o  
libs/filesystem/src/path.cpp:389:34: error: out-of-line definition of 'replace_extension_v3' does not match any declaration in 'boost::filesystem::path'  
BOOST_FILESYSTEM_DECL void path::replace_extension_v3(path const& new_extension)  
                                 ^~~~~~~~~~~~~~~~~~~~  
libs/filesystem/src/path.cpp:404:34: error: out-of-line definition of 'replace_extension_v4' does not match any declaration in 'boost::filesystem::path'  
BOOST_FILESYSTEM_DECL void path::replace_extension_v4(path const& new_extension)  
                                 ^~~~~~~~~~~~~~~~~~~~  
libs/filesystem/src/path.cpp:407:45: error: use of undeclared identifier 'find_extension_v4_size'; did you mean 'find_filename_v4_size'?  
    size_type ext_pos = m_pathname.size() - find_extension_v4_size();  
                                            ^~~~~~~~~~~~~~~~~~~~~~  
                                            find_filename_v4_size  
/usr/local/include/boost/filesystem/path.hpp:936:50: note: 'find_filename_v4_size' declared here  
    BOOST_FILESYSTEM_DECL string_type::size_type find_filename_v4_size() const;  
                                                 ^  
libs/filesystem/src/path.cpp:582:52: error: out-of-line definition of 'find_extension_v4_size' does not match any declaration in 'boost::filesystem::path'  
BOOST_FILESYSTEM_DECL string_type::size_type path::find_extension_v4_size() const  
                                                   ^~~~~~~~~~~~~~~~~~~~~~  
4 errors generated.  
  
    "clang++" -x c++ -std=c++17 -fvisibility-inlines-hidden -O3 -Wall -fvisibility=hidden -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW -DBOOST_FILESYSTEM_HAS_INIT_PRIORITY -DBOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC -DBOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC -DBOOST_FILESYSTEM_NO_CXX20_ATOMIC_REF -DBOOST_FILESYSTEM_SOURCE -DBOOST_FILESYSTEM_STATIC_LINK=1 -DNDEBUG -I"." -I"libs/filesystem/src"  -c -o "bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/path.o" "libs/filesystem/src/path.cpp"  
  
...failed clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/path.o...  
...skipped <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden>libboost_filesystem.a(clean) for lack of <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden>codecvt_error_category.o...  
...skipped <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden>libboost_filesystem.a for lack of <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden>codecvt_error_category.o...  
...skipped <p/Users/mborland/Downloads/boost_1_79_0/stage/lib>libboost_filesystem.a for lack of <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden>libboost_filesystem.a...  
...skipped <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden>libboost_filesystem-variant-static.cmake for lack of <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden>libboost_filesystem.a...  
...skipped <p/Users/mborland/Downloads/boost_1_79_0/stage/lib/cmake/boost_filesystem-1.79.0>libboost_filesystem-variant-static.cmake for lack of <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden>libboost_filesystem-variant-static.cmake...  
clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden/path.o  
libs/filesystem/src/path.cpp:389:34: error: out-of-line definition of 'replace_extension_v3' does not match any declaration in 'boost::filesystem::path'  
BOOST_FILESYSTEM_DECL void path::replace_extension_v3(path const& new_extension)  
                                 ^~~~~~~~~~~~~~~~~~~~  
libs/filesystem/src/path.cpp:404:34: error: out-of-line definition of 'replace_extension_v4' does not match any declaration in 'boost::filesystem::path'  
BOOST_FILESYSTEM_DECL void path::replace_extension_v4(path const& new_extension)  
                                 ^~~~~~~~~~~~~~~~~~~~  
libs/filesystem/src/path.cpp:407:45: error: use of undeclared identifier 'find_extension_v4_size'; did you mean 'find_filename_v4_size'?  
    size_type ext_pos = m_pathname.size() - find_extension_v4_size();  
                                            ^~~~~~~~~~~~~~~~~~~~~~  
                                            find_filename_v4_size  
/usr/local/include/boost/filesystem/path.hpp:936:50: note: 'find_filename_v4_size' declared here  
    BOOST_FILESYSTEM_DECL string_type::size_type find_filename_v4_size() const;  
                                                 ^  
libs/filesystem/src/path.cpp:582:52: error: out-of-line definition of 'find_extension_v4_size' does not match any declaration in 'boost::filesystem::path'  
BOOST_FILESYSTEM_DECL string_type::size_type path::find_extension_v4_size() const  
                                                   ^~~~~~~~~~~~~~~~~~~~~~  
4 errors generated.  
  
    "clang++" -x c++ -std=c++17 -fvisibility-inlines-hidden -fPIC -O3 -Wall -fvisibility=hidden -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW -DBOOST_FILESYSTEM_HAS_INIT_PRIORITY -DBOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC -DBOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC -DBOOST_FILESYSTEM_NO_CXX20_ATOMIC_REF -DBOOST_FILESYSTEM_SOURCE -DNDEBUG -I"." -I"libs/filesystem/src"  -c -o "bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden/path.o" "libs/filesystem/src/path.cpp"  
  
...failed clang-darwin.compile.c++ bin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden/path.o...  
...skipped <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden>libboost_filesystem.dylib for lack of <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden>codecvt_error_category.o...  
...skipped <pbin.v2/libs/fiber/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden>libboost_fiber.dylib for lack of <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden>libboost_filesystem.dylib...  
...skipped <pbin.v2/libs/fiber/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden>libboost_fiber-variant-shared.cmake for lack of <pbin.v2/libs/fiber/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden>libboost_fiber.dylib...  
...skipped <p/Users/mborland/Downloads/boost_1_79_0/stage/lib/cmake/boost_fiber-1.79.0>libboost_fiber-variant-shared.cmake for lack of <pbin.v2/libs/fiber/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden>libboost_fiber-variant-shared.cmake...  
...skipped <p/Users/mborland/Downloads/boost_1_79_0/stage/lib>libboost_filesystem.dylib for lack of <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden>libboost_filesystem.dylib...  
...skipped <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden>libboost_filesystem-variant-shared.cmake for lack of <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden>libboost_filesystem.dylib...  
...skipped <p/Users/mborland/Downloads/boost_1_79_0/stage/lib/cmake/boost_filesystem-1.79.0>libboost_filesystem-variant-shared.cmake for lack of <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden>libboost_filesystem-variant-shared.cmake...  
...skipped <pbin.v2/libs/wave/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_wave.dylib for lack of <pbin.v2/libs/filesystem/build/clang-darwin-13/release/cxxstd-17-iso/threading-multi/visibility-hidden>libboost_filesystem.dylib...  
...skipped <p/Users/mborland/Downloads/boost_1_79_0/stage/lib>libboost_wave.dylib for lack of <pbin.v2/libs/wave/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_wave.dylib...  
...skipped <pbin.v2/libs/wave/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_wave-variant-shared.cmake for lack of <pbin.v2/libs/wave/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_wave.dylib...  
...skipped <p/Users/mborland/Downloads/boost_1_79_0/stage/lib/cmake/boost_wave-1.79.0>libboost_wave-variant-shared.cmake for lack of <pbin.v2/libs/wave/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_wave-variant-shared.cmake...  
clang-darwin.compile.c++ bin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threadapi-pthread/threading-multi/visibility-hidden/text_file_backend.o  
libs/log/src/text_file_backend.cpp:698:68: error: only virtual member functions can be marked 'override'  
        bool is_in_storage(filesystem::path const& src_path) const BOOST_OVERRIDE;  
                                                                   ^~~~~~~~~~~~~~  
/usr/local/include/boost/config/detail/suffix.hpp:687:26: note: expanded from macro 'BOOST_OVERRIDE'  
#  define BOOST_OVERRIDE override  
                         ^  
libs/log/src/text_file_backend.cpp:701:15: error: no type named 'scan_result' in namespace 'boost::log::sinks::file'  
        file::scan_result scan_for_files(file::scan_method method, filesystem::path const& pattern) BOOST_OVERRIDE;  
        ~~~~~~^  
libs/log/src/text_file_backend.cpp:948:11: error: no type named 'scan_result' in namespace 'boost::log::sinks::file'  
    file::scan_result file_collector::scan_for_files(file::scan_method method, filesystem::path const& pattern)  
    ~~~~~~^  
libs/log/src/text_file_backend.cpp:950:15: error: no type named 'scan_result' in namespace 'boost::log::sinks::file'  
        file::scan_result result;  
        ~~~~~~^  
libs/log/src/text_file_backend.cpp:1442:78: error: no member named 'is_in_storage' in 'boost::log::sinks::file::collector'  
                        increment_file_counter = !m_pImpl->m_pFileCollector->is_in_storage(last_file_name);  
                                                  ~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
libs/log/src/text_file_backend.cpp:1673:11: error: no type named 'scan_result' in namespace 'boost::log::sinks::file'  
    file::scan_result result = m_pImpl->m_pFileCollector->scan_for_files  
    ~~~~~~^  
In file included from libs/log/src/text_file_backend.cpp:36:  
/usr/local/include/boost/smart_ptr/make_shared_object.hpp:256:17: error: allocating an object of abstract class type 'boost::log::sinks::anonymous::file_collector'  
    ::new( pv ) T( boost::detail::sp_forward<Args>( args )... );  
                ^  
libs/log/src/text_file_backend.cpp:1044:24: note: in instantiation of function template specialization 'boost::make_shared<boost::log::sinks::anonymous::file_collector, boost::shared_ptr<boost::log::sinks::anonymous::file_collector_repository> &, const boost::filesystem::path &, unsigned long &, unsigned long &, unsigned long &>' requested here  
            p = boost::make_shared< file_collector >(  
                       ^  
/usr/local/include/boost/log/sinks/text_file_backend.hpp:124:23: note: unimplemented pure virtual method 'scan_for_files' in 'file_collector'  
    virtual uintmax_t scan_for_files(  
                      ^  
7 errors generated.  
  
    "clang++" -x c++ -std=c++17 -fvisibility-inlines-hidden -O3 -Wall -fvisibility=hidden -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_WITHOUT_DEBUG_OUTPUT -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_LIB=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_LIB=1 -DNDEBUG -D__STDC_CONSTANT_MACROS -I"." -I"libs/log/src"  -c -o "bin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threadapi-pthread/threading-multi/visibility-hidden/text_file_backend.o" "libs/log/src/text_file_backend.cpp"  
  
...failed clang-darwin.compile.c++ bin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threadapi-pthread/threading-multi/visibility-hidden/text_file_backend.o...  
...skipped <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_log.a(clean) for lack of <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threadapi-pthread/threading-multi/visibility-hidden>text_file_backend.o...  
...skipped <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_log.a for lack of <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threadapi-pthread/threading-multi/visibility-hidden>text_file_backend.o...  
...skipped <p/Users/mborland/Downloads/boost_1_79_0/stage/lib>libboost_log.a for lack of <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_log.a...  
...skipped <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_log-variant-static.cmake for lack of <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_log.a...  
...skipped <p/Users/mborland/Downloads/boost_1_79_0/stage/lib/cmake/boost_log-1.79.0>libboost_log-variant-static.cmake for lack of <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_log-variant-static.cmake...  
clang-darwin.compile.c++ bin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden/text_file_backend.o  
libs/log/src/text_file_backend.cpp:698:68: error: only virtual member functions can be marked 'override'  
        bool is_in_storage(filesystem::path const& src_path) const BOOST_OVERRIDE;  
                                                                   ^~~~~~~~~~~~~~  
/usr/local/include/boost/config/detail/suffix.hpp:687:26: note: expanded from macro 'BOOST_OVERRIDE'  
#  define BOOST_OVERRIDE override  
                         ^  
libs/log/src/text_file_backend.cpp:701:15: error: no type named 'scan_result' in namespace 'boost::log::sinks::file'  
        file::scan_result scan_for_files(file::scan_method method, filesystem::path const& pattern) BOOST_OVERRIDE;  
        ~~~~~~^  
libs/log/src/text_file_backend.cpp:948:11: error: no type named 'scan_result' in namespace 'boost::log::sinks::file'  
    file::scan_result file_collector::scan_for_files(file::scan_method method, filesystem::path const& pattern)  
    ~~~~~~^  
libs/log/src/text_file_backend.cpp:950:15: error: no type named 'scan_result' in namespace 'boost::log::sinks::file'  
        file::scan_result result;  
        ~~~~~~^  
libs/log/src/text_file_backend.cpp:1442:78: error: no member named 'is_in_storage' in 'boost::log::sinks::file::collector'  
                        increment_file_counter = !m_pImpl->m_pFileCollector->is_in_storage(last_file_name);  
                                                  ~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
libs/log/src/text_file_backend.cpp:1673:11: error: no type named 'scan_result' in namespace 'boost::log::sinks::file'  
    file::scan_result result = m_pImpl->m_pFileCollector->scan_for_files  
    ~~~~~~^  
In file included from libs/log/src/text_file_backend.cpp:36:  
/usr/local/include/boost/smart_ptr/make_shared_object.hpp:256:17: error: allocating an object of abstract class type 'boost::log::sinks::anonymous::file_collector'  
    ::new( pv ) T( boost::detail::sp_forward<Args>( args )... );  
                ^  
libs/log/src/text_file_backend.cpp:1044:24: note: in instantiation of function template specialization 'boost::make_shared<boost::log::sinks::anonymous::file_collector, boost::shared_ptr<boost::log::sinks::anonymous::file_collector_repository> &, const boost::filesystem::path &, unsigned long &, unsigned long &, unsigned long &>' requested here  
            p = boost::make_shared< file_collector >(  
                       ^  
/usr/local/include/boost/log/sinks/text_file_backend.hpp:124:23: note: unimplemented pure virtual method 'scan_for_files' in 'file_collector'  
    virtual uintmax_t scan_for_files(  
                      ^  
7 errors generated.  
  
    "clang++" -x c++ -std=c++17 -fvisibility-inlines-hidden -fPIC -O3 -Wall -fvisibility=hidden -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_DLL -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_WITHOUT_DEBUG_OUTPUT -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DNDEBUG -D__STDC_CONSTANT_MACROS -I"." -I"libs/log/src"  -c -o "bin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden/text_file_backend.o" "libs/log/src/text_file_backend.cpp"  
  
...failed clang-darwin.compile.c++ bin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden/text_file_backend.o...  
...skipped <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_log.dylib for lack of <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>text_file_backend.o...  
...skipped <p/Users/mborland/Downloads/boost_1_79_0/stage/lib>libboost_log.dylib for lack of <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_log.dylib...  
...skipped <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup.dylib for lack of <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_log.dylib...  
...skipped <p/Users/mborland/Downloads/boost_1_79_0/stage/lib>libboost_log_setup.dylib for lack of <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup.dylib...  
...skipped <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup-variant-shared.cmake for lack of <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup.dylib...  
...skipped <p/Users/mborland/Downloads/boost_1_79_0/stage/lib/cmake/boost_log_setup-1.79.0>libboost_log_setup-variant-shared.cmake for lack of <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup-variant-shared.cmake...  
...skipped <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_log-variant-shared.cmake for lack of <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_log.dylib...  
...skipped <p/Users/mborland/Downloads/boost_1_79_0/stage/lib/cmake/boost_log-1.79.0>libboost_log-variant-shared.cmake for lack of <pbin.v2/libs/log/build/clang-darwin-13/release/cxxstd-17-iso/threadapi-pthread/threading-multi/visibility-hidden>libboost_log-variant-shared.cmake...  
...failed updating 10 targets...  
...skipped 29 targets...  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2022-03-11 11:07:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/231#issuecomment-1065011219  

Your build apparently is picking up headers from `/usr/local/include`, which are from a different Boost version.
