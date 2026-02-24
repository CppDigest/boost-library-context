# #763 - architecture=arm+x86: clang error cannot use 'precompiled-header' output with multiple -arch options [Closed]

> Username: facetheheat  
> Created at: 2023-08-15 17:04:11 UTC  
> Updated at: 2023-08-15 17:06:14 UTC  
> Closed at: 2023-08-15 17:06:13 UTC  
> Url: https://github.com/boostorg/build/issues/763  

Version 1_82_0  
Host: Apple Silicon M1 (mac mini)  
  
$ ./bootstrap.sh --prefix=/tmp/build_universal --without-icu  
###  
###  
### Using 'clang' toolset.  
###  
###  
Apple clang version 14.0.3 (clang-1403.0.22.14.1)  
Target: arm64-apple-darwin22.6.0  
Thread model: posix  
  
  
$ ./b2 -j8 release architecture=arm+x86 address-model=64 -a cxxstd=14 cxxflags=-mmacosx-version-min=10.13 link=static runtime-link=shared threading=multi boost.locale.icu=off install --prefix=/tmp/build_universal  
```  
  
[2023-08-15T16:41:58.937Z]     - default address-model    : 64-bit [1]  
[2023-08-15T16:41:58.937Z]     - default architecture     : arm [1]  
[2023-08-15T16:42:02.227Z] error: No best alternative for libs/context/build/asm_sources  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>qcc  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>mach-o <threading>multi <toolset>clang  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>mach-o <threading>multi <toolset>darwin  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>pe <threading>multi <toolset>msvc  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>elf <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>mach-o <threading>multi <toolset>clang  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>mach-o <threading>multi <toolset>darwin  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>pe <threading>multi <toolset>msvc  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>loongarch <binary-format>elf <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>o32 <address-model>32 <architecture>mips <binary-format>elf <threading>multi <toolset>clang  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>o32 <address-model>32 <architecture>mips <binary-format>elf <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>n64 <address-model>64 <architecture>mips <binary-format>elf <threading>multi <toolset>clang  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>n64 <address-model>64 <architecture>mips <binary-format>elf <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>elf <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>mach-o <threading>multi <toolset>darwin  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>xcoff <threading>multi <toolset>clang  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>xcoff <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>elf <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>mach-o <threading>multi <toolset>clang  
[2023-08-15T16:42:02.227Z]         not matched  
[2023-08-15T16:42:02.227Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>mach-o <threading>multi <toolset>darwin  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>xcoff <threading>multi <toolset>clang  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>xcoff <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>32_64 <architecture>power <binary-format>mach-o <threading>multi  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>riscv <binary-format>elf <threading>multi <toolset>clang  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>riscv <binary-format>elf <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>s390x <binary-format>elf <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>s390x <binary-format>elf <threading>multi <toolset>clang  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>clang  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>darwin  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang-win  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>intel  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>msvc  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>embarcadero  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>borland  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>clang  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>darwin  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>intel  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang-win  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>intel  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>msvc  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>embarcadero  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>x32 <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>x32 <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>x32 <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <address-model>32_64 <architecture>x86 <binary-format>mach-o <threading>multi  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:02.228Z]     next alternative: required properties: <abi>sysv <architecture>combined <binary-format>mach-o <threading>multi  
[2023-08-15T16:42:02.228Z]         not matched  
[2023-08-15T16:42:03.814Z]     - has std::atomic_ref      : no [2]  
[2023-08-15T16:42:03.814Z]     - has statx                : no [2]  
[2023-08-15T16:42:03.814Z]     - has statx syscall        : no [2]  
[2023-08-15T16:42:03.814Z]     - has init_priority attribute : yes [2]  
[2023-08-15T16:42:04.075Z]     - has stat::st_blksize     : no [2]  
[2023-08-15T16:42:04.075Z]     - has stat::st_mtim        : no [2]  
[2023-08-15T16:42:04.075Z]     - has stat::st_mtimensec   : no [2]  
[2023-08-15T16:42:04.075Z]     - has stat::st_mtimespec   : yes [2]  
[2023-08-15T16:42:04.334Z]     - has stat::st_birthtim    : no [2]  
[2023-08-15T16:42:04.334Z]     - has stat::st_birthtimensec : no [2]  
[2023-08-15T16:42:04.334Z]     - has stat::st_birthtimespec : yes [2]  
[2023-08-15T16:42:04.594Z]     - has fdopendir(O_NOFOLLOW) : yes [2]  
[2023-08-15T16:42:04.594Z]     - has POSIX *at APIs       : no [2]  
[2023-08-15T16:42:04.854Z]     - cxx11_auto_declarations  : yes [2]  
[2023-08-15T16:42:04.854Z]     - cxx11_constexpr          : yes [2]  
[2023-08-15T16:42:05.113Z]     - cxx11_defaulted_functions : yes [2]  
[2023-08-15T16:42:05.113Z]     - cxx11_final              : yes [2]  
[2023-08-15T16:42:05.113Z]     - cxx11_hdr_mutex          : yes [2]  
[2023-08-15T16:42:05.374Z]     - cxx11_hdr_tuple          : yes [2]  
[2023-08-15T16:42:05.374Z]     - cxx11_lambdas            : yes [2]  
[2023-08-15T16:42:05.374Z]     - cxx11_noexcept           : yes [2]  
[2023-08-15T16:42:05.633Z]     - cxx11_nullptr            : yes [2]  
[2023-08-15T16:42:05.633Z]     - cxx11_rvalue_references  : yes [2]  
[2023-08-15T16:42:05.890Z]     - cxx11_template_aliases   : yes [2]  
[2023-08-15T16:42:05.890Z]     - cxx11_thread_local       : yes [2]  
[2023-08-15T16:42:05.890Z]     - cxx11_variadic_templates : yes [2]  
[2023-08-15T16:42:05.890Z]     - cxx11_auto_declarations  : yes [3]  
[2023-08-15T16:42:05.890Z]     - cxx11_constexpr          : yes [3]  
[2023-08-15T16:42:05.890Z]     - cxx11_defaulted_functions : yes [3]  
[2023-08-15T16:42:05.890Z]     - cxx11_final              : yes [3]  
[2023-08-15T16:42:05.890Z]     - cxx11_hdr_mutex          : yes [3]  
[2023-08-15T16:42:05.890Z]     - cxx11_hdr_tuple          : yes [3]  
[2023-08-15T16:42:05.890Z]     - cxx11_lambdas            : yes [3]  
[2023-08-15T16:42:05.890Z]     - cxx11_noexcept           : yes [3]  
[2023-08-15T16:42:05.890Z]     - cxx11_nullptr            : yes [3]  
[2023-08-15T16:42:05.890Z]     - cxx11_rvalue_references  : yes [3]  
[2023-08-15T16:42:05.890Z]     - cxx11_template_aliases   : yes [3]  
[2023-08-15T16:42:05.890Z]     - cxx11_thread_local       : yes [3]  
[2023-08-15T16:42:05.890Z]     - cxx11_variadic_templates : yes [3]  
[2023-08-15T16:42:07.269Z]     - has_icu builds           : no [2]  
[2023-08-15T16:42:09.176Z] warning: Graph library does not contain MPI-based parallel components.  
[2023-08-15T16:42:09.176Z] note: to enable them, add "using mpi ;" to your user-config.jam.  
[2023-08-15T16:42:09.176Z] note: to suppress this message, pass "--without-graph_parallel" to bjam.  
[2023-08-15T16:42:09.437Z]     - zlib                     : yes [4]  
[2023-08-15T16:42:10.116Z]     - bzip2                    : yes [4]  
[2023-08-15T16:42:10.116Z]     - lzma                     : no [4]  
[2023-08-15T16:42:11.496Z]     - zstd                     : no [4]  
[2023-08-15T16:42:11.496Z]     - has_lzma_cputhreads builds : no [2]  
[2023-08-15T16:42:11.756Z]     - cxx11_decltype           : yes [2]  
[2023-08-15T16:42:12.015Z]     - cxx11_basic_alignas      : yes [2]  
[2023-08-15T16:42:12.954Z]     - iconv (libc)             : no [2]  
[2023-08-15T16:42:12.954Z]     - iconv (separate)         : yes [2]  
[2023-08-15T16:42:12.954Z]     - cxx11_defaulted_moves    : yes [2]  
[2023-08-15T16:42:13.424Z]     - cxx11_hdr_functional     : yes [2]  
[2023-08-15T16:42:13.424Z]     - cxx11_hdr_type_traits    : yes [2]  
[2023-08-15T16:42:13.424Z]     - cxx11_override           : yes [2]  
[2023-08-15T16:42:13.424Z]     - cxx11_range_based_for    : yes [2]  
[2023-08-15T16:42:13.424Z]     - cxx11_scoped_enums       : yes [2]  
[2023-08-15T16:42:13.683Z]     - cxx11_smart_ptr          : yes [2]  
[2023-08-15T16:42:13.683Z]     - cxx11_static_assert      : yes [2]  
[2023-08-15T16:42:14.254Z]     - native atomic int32 supported : yes [2]  
[2023-08-15T16:42:14.514Z]     - native syslog supported  : yes [2]  
[2023-08-15T16:42:14.775Z]     - pthread supports robust mutexes : no [2]  
[2023-08-15T16:42:15.341Z]     - lockfree boost::atomic_flag : yes [2]  
[2023-08-15T16:42:21.909Z]     - gcc visibility           : yes [2]  
[2023-08-15T16:42:21.909Z]     - sfinae_expr              : yes [2]  
[2023-08-15T16:42:21.909Z]     - cxx11_unified_initialization_syntax : yes [2]  
[2023-08-15T16:42:21.909Z]     - cxx11_hdr_initializer_list : yes [2]  
[2023-08-15T16:42:21.909Z]     - cxx11_hdr_chrono         : yes [2]  
[2023-08-15T16:42:22.169Z]     - cxx11_numeric_limits     : yes [2]  
[2023-08-15T16:42:22.169Z]     - cxx11_hdr_array          : yes [2]  
[2023-08-15T16:42:22.169Z]     - cxx11_hdr_atomic         : yes [2]  
[2023-08-15T16:42:22.428Z]     - cxx11_allocator          : yes [2]  
[2023-08-15T16:42:22.428Z]     - cxx11_explicit_conversion_operators : yes [2]  
[2023-08-15T16:42:22.997Z]     - long double support      : yes [2]  
[2023-08-15T16:42:23.568Z] warning: skipping optional Message Passing Interface (MPI) library.  
[2023-08-15T16:42:23.568Z] note: to enable MPI support, add "using mpi ;" to user-config.jam.  
[2023-08-15T16:42:23.568Z] note: to suppress this message, pass "--without-mpi" to bjam.  
[2023-08-15T16:42:23.568Z] note: otherwise, you can safely ignore this message.  
[2023-08-15T16:42:24.947Z]     - std::fstream is moveable and swappable : yes [2]  
[2023-08-15T16:42:25.207Z]     - Has Large File Support   : yes [2]  
[2023-08-15T16:42:25.207Z]     - Has attribute init_priority : yes [2]  
[2023-08-15T16:42:26.586Z]     - libbacktrace builds      : no [2]  
[2023-08-15T16:42:26.586Z]     - libbacktrace builds      : no [3]  
[2023-08-15T16:42:27.968Z]     - addr2line builds         : yes [2]  
[2023-08-15T16:42:27.968Z]     - WinDbg builds            : no [2]  
[2023-08-15T16:42:27.968Z]     - WinDbg builds            : no [3]  
[2023-08-15T16:42:28.228Z]     - WinDbgCached builds      : no [2]  
[2023-08-15T16:42:28.228Z]     - WinDbgCached builds      : no [3]  
[2023-08-15T16:42:28.798Z]     - BOOST_COMP_GNUC >= 4.3.0 : no [2]  
[2023-08-15T16:42:30.726Z]     - cxx11_hdr_thread         : yes [2]  
[2023-08-15T16:42:30.726Z]     - cxx11_hdr_regex          : yes [2]  
[2023-08-15T16:42:36.005Z]   
[2023-08-15T16:42:36.005Z] [1] clang-14  
[2023-08-15T16:42:36.005Z] [2] clang-darwin-14/release/architecture-arm+x86/boost.locale.icu-off/cxxstd-14-iso/link-static/python-2.7/threadapi-pthread/threading-multi/visibility-hidden  
[2023-08-15T16:42:36.005Z] [3] clang-darwin-14/release/architecture-arm+x86/boost.locale.icu-off/build-no/cxxstd-14-iso/link-static/python-2.7/threadapi-pthread/threading-multi/visibility-hidden  
[2023-08-15T16:42:36.005Z] [4] link-static  
[2023-08-15T16:42:36.005Z]   
[2023-08-15T16:42:36.005Z] Component configuration:  
[2023-08-15T16:42:36.005Z]   
[2023-08-15T16:42:36.005Z]     - atomic                   : building  
[2023-08-15T16:42:36.005Z]     - chrono                   : building  
[2023-08-15T16:42:36.005Z]     - container                : building  
[2023-08-15T16:42:36.005Z]     - context                  : building  
[2023-08-15T16:42:36.005Z]     - contract                 : building  
[2023-08-15T16:42:36.006Z]     - coroutine                : building  
[2023-08-15T16:42:36.006Z]     - date_time                : building  
[2023-08-15T16:42:36.006Z]     - exception                : building  
[2023-08-15T16:42:36.006Z]     - fiber                    : building  
[2023-08-15T16:42:36.006Z]     - filesystem               : building  
[2023-08-15T16:42:36.006Z]     - graph                    : building  
[2023-08-15T16:42:36.006Z]     - graph_parallel           : building  
[2023-08-15T16:42:36.006Z]     - headers                  : building  
[2023-08-15T16:42:36.006Z]     - iostreams                : building  
[2023-08-15T16:42:36.006Z]     - json                     : building  
[2023-08-15T16:42:36.006Z]     - locale                   : building  
[2023-08-15T16:42:36.006Z]     - log                      : building  
[2023-08-15T16:42:36.006Z]     - math                     : building  
[2023-08-15T16:42:36.006Z]     - mpi                      : building  
[2023-08-15T16:42:36.006Z]     - nowide                   : building  
[2023-08-15T16:42:36.006Z]     - program_options          : building  
[2023-08-15T16:42:36.006Z]     - python                   : building  
[2023-08-15T16:42:36.006Z]     - random                   : building  
[2023-08-15T16:42:36.006Z]     - regex                    : building  
[2023-08-15T16:42:36.006Z]     - serialization            : building  
[2023-08-15T16:42:36.006Z]     - stacktrace               : building  
[2023-08-15T16:42:36.006Z]     - system                   : building  
[2023-08-15T16:42:36.006Z]     - test                     : building  
[2023-08-15T16:42:36.006Z]     - thread                   : building  
[2023-08-15T16:42:36.006Z]     - timer                    : building  
[2023-08-15T16:42:36.006Z]     - type_erasure             : building  
[2023-08-15T16:42:36.006Z]     - url                      : building  
[2023-08-15T16:42:36.006Z]     - wave                     : building  
[2023-08-15T16:42:36.006Z]   
[2023-08-15T16:42:36.006Z] ...patience...  
[2023-08-15T16:42:36.006Z] ...patience...  
[2023-08-15T16:42:36.006Z] ...patience...  
[2023-08-15T16:42:36.265Z] ...patience...  
[2023-08-15T16:42:36.524Z] ...patience...  
[2023-08-15T16:42:37.911Z] ...patience...  
[2023-08-15T16:42:37.911Z] ...found 48992 targets...  
[2023-08-15T16:42:37.911Z] ...updating 18062 targets...  
```  
  
  
Failed message:  
```  
[2023-08-15T16:43:29.919Z] clang-darwin.compile.c++.pch bin.v2/libs/math/build/clang-darwin-14/release/architecture-arm+x86/cxxstd-14-iso/link-static/threading-multi/visibility-hidden/pch.pch  
[2023-08-15T16:43:29.919Z] clang: error: cannot use 'precompiled-header' output with multiple -arch options  
[2023-08-15T16:43:29.919Z]   
```  
  
Build status  
```  
[2023-08-15T16:45:49.967Z] boost-install.generate-cmake-variant- bin.v2/libs/atomic/build/clang-darwin-14/release/architecture-arm+x86/cxxstd-14-iso/link-static/threading-multi/visibility-hidden/libboost_atomic-variant-static.cmake  
[2023-08-15T16:45:49.967Z] common.copy /Users/imdesktop/im-builder/workspace/EXTERNALS/IM_Externals_macOS/build_boost/boost_1_82_0/build_universal/lib/libboost_atomic.a  
[2023-08-15T16:45:50.237Z] common.copy /Users/imdesktop/im-builder/workspace/EXTERNALS/IM_Externals_macOS/build_boost/boost_1_82_0/build_universal/lib/cmake/boost_atomic-1.82.0/libboost_atomic-variant-static.cmake  
[2023-08-15T16:45:51.189Z] clang-darwin.compile.c++ bin.v2/libs/chrono/build/clang-darwin-14/release/architecture-arm+x86/cxxstd-14-iso/link-static/threading-multi/visibility-hidden/process_cpu_clocks.o  
[2023-08-15T16:45:51.189Z] clang-darwin.compile.c++ bin.v2/libs/chrono/build/clang-darwin-14/release/architecture-arm+x86/cxxstd-14-iso/link-static/threading-multi/visibility-hidden/thread_clock.o  
[2023-08-15T16:45:51.189Z] clang-darwin.compile.c++ bin.v2/libs/chrono/build/clang-darwin-14/release/architecture-arm+x86/cxxstd-14-iso/link-static/threading-multi/visibility-hidden/chrono.o  
[2023-08-15T16:45:51.189Z] clang-darwin.archive bin.v2/libs/chrono/build/clang-darwin-14/release/architecture-arm+x86/cxxstd-14-iso/link-static/threading-multi/visibility-hidden/libboost_chrono.a  
[2023-08-15T16:45:51.189Z] warning: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ranlib: archive library: bin.v2/libs/chrono/build/clang-darwin-14/release/architecture-arm+x86/cxxstd-14-iso/link-static/threading-multi/visibility-hidden/libboost_chrono.a will be fat and ar(1) will not be able to operate on it  
[2023-08-15T16:45:51.189Z] boost-install.generate-cmake-variant- bin.v2/libs/chrono/build/clang-darwin-14/release/architecture-arm+x86/cxxstd-14-iso/link-static/threading-multi/visibility-hidden/libboost_chrono-variant-static.cmake  
[2023-08-15T16:45:51.189Z] common.copy /Users/imdesktop/im-builder/workspace/EXTERNALS/IM_Externals_macOS/build_boost/boost_1_82_0/build_universal/lib/libboost_chrono.a  
[2023-08-15T16:45:51.189Z] common.copy /Users/imdesktop/im-builder/workspace/EXTERNALS/IM_Externals_macOS/build_boost/boost_1_82_0/build_universal/lib/cmake/boost_chrono-1.82.0/libboost_chrono-variant-static.cmake  
[2023-08-15T16:45:51.657Z] ...failed updating 1 target...  
[2023-08-15T16:45:51.657Z] ...skipped 156 targets...  
[2023-08-15T16:45:51.657Z] ...updated 17905 targets...  
```

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2023-08-15 17:06:12 UTC  
> Url: https://github.com/boostorg/build/issues/763#issuecomment-1679303996  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
