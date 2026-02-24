# #404 OpenBSD Fix. [Closed]

> Username: ghost  
> Created at: 2024-09-27 04:19:51 UTC  
> Updated at: 2024-09-30 03:27:57 UTC  
> Closed at: 2024-09-30 03:27:56 UTC  
> Url: https://github.com/boostorg/process/pull/404  

WIP.  
  
Accidentally conflated my DragonFlyBSD CWD from PID fix. I can remove that if you want.  
  
```  
openbsd$ cd boost/libs/process/test/v2  
openbsd$ ../../../../b2  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
    - symlinks supported       : yes (cached)  
    - has -Wl,--no-undefined   : no  (cached) [2]  
    - has -Wl,-undefined,error : no  (cached) [2]  
    - has statx                : no  (cached) [2]  
    - has statx syscall        : no  (cached) [2]  
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
    - has stat::st_mtimensec   : yes (cached) [2]  
    - has stat::st_mtimespec   : yes (cached) [2]  
    - has stat::st_birthtim    : no  (cached) [2]  
    - has stat::st_birthtimensec : no  (cached) [2]  
    - has stat::st_birthtimespec : no  (cached) [2]  
    - has fdopendir(O_NOFOLLOW) : yes (cached) [2]  
    - has dirent::d_type       : yes (cached) [2]  
    - has POSIX *at APIs       : no  (cached) [2]  
    - has fallocate            : no  (cached) [2]  
    - BOOST_COMP_GNUC >= 4.3.0 : no  (cached) [2]  
  
[1] clang-13  
[2] clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-hidden  
...patience...  
...patience...  
...found 4604 targets...  
...updating 25 targets...  
clang-linux.compile.c++ ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/shell.o  
clang-linux.link.dll ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost/target  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::get_shell_category() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::shell::~shell() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::ext::env(int, boost::system::error_code&) [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::detail::ext::native_env_handle_deleter::operator()(char*) const [--no-allow-shlib-undefined]  
clang++: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++"  -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -o "../../../../bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost/target" -Wl,--start-group "../../../../bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost/target.o" "../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0" "../../../../bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_system.so.1.87.0" "../../../../bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_filesystem.so.1.87.0"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -fPIC -g -m64 -lkvm  
  
...failed clang-linux.link ../../../../bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost/target...  
clang-linux.link.dll ../../../../bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost/libtest_impl.so.1.87.0  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/utf8.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/utf8  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::get_shell_category() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::shell::~shell() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::ext::env(int, boost::system::error_code&) [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::detail::ext::native_env_handle_deleter::operator()(char*) const [--no-allow-shlib-undefined]  
clang++: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++"  -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -o "../../../../bin.v2/libs/process/test/v2/utf8.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/utf8" -Wl,--start-group "../../../../bin.v2/libs/process/test/v2/utf8.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/utf8.o" "../../../../bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost/libtest_impl.so.1.87.0" "../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0" "../../../../bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_system.so.1.87.0" "../../../../bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_filesystem.so.1.87.0"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -fPIC -g -m64 -lkvm  
  
...failed clang-linux.link ../../../../bin.v2/libs/process/test/v2/utf8.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/utf8...  
...skipped <p../../../../bin.v2/libs/process/test/v2/utf8.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>utf8.run for lack of <p../../../../bin.v2/libs/process/test/v2/utf8.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>utf8...  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/environment.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/environment  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::get_shell_category() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::shell::~shell() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::ext::env(int, boost::system::error_code&) [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::detail::ext::native_env_handle_deleter::operator()(char*) const [--no-allow-shlib-undefined]  
clang++: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++"  -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -o "../../../../bin.v2/libs/process/test/v2/environment.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/environment" -Wl,--start-group "../../../../bin.v2/libs/process/test/v2/environment.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/environment.o" "../../../../bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost/libtest_impl.so.1.87.0" "../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0" "../../../../bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_system.so.1.87.0" "../../../../bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_filesystem.so.1.87.0"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -fPIC -g -m64 -lkvm  
  
...failed clang-linux.link ../../../../bin.v2/libs/process/test/v2/environment.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/environment...  
...skipped <p../../../../bin.v2/libs/process/test/v2/environment.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>environment.run for lack of <p../../../../bin.v2/libs/process/test/v2/environment.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>environment...  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/cstring_ref.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/cstring_ref  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::get_shell_category() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::shell::~shell() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::ext::env(int, boost::system::error_code&) [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::detail::ext::native_env_handle_deleter::operator()(char*) const [--no-allow-shlib-undefined]  
clang++: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++"  -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -o "../../../../bin.v2/libs/process/test/v2/cstring_ref.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/cstring_ref" -Wl,--start-group "../../../../bin.v2/libs/process/test/v2/cstring_ref.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/cstring_ref.o" "../../../../bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost/libtest_impl.so.1.87.0" "../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0" "../../../../bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_system.so.1.87.0" "../../../../bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_filesystem.so.1.87.0"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -fPIC -g -m64 -lkvm  
  
...failed clang-linux.link ../../../../bin.v2/libs/process/test/v2/cstring_ref.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/cstring_ref...  
...skipped <p../../../../bin.v2/libs/process/test/v2/cstring_ref.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>cstring_ref.run for lack of <p../../../../bin.v2/libs/process/test/v2/cstring_ref.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>cstring_ref...  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/shell  
ld: error: undefined symbol: boost::process::v2::get_shell_category()  
>>> referenced by shell.hpp:21 (../../../../boost/process/v2/shell.hpp:21)  
>>>               ../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/shell.o:(__cxx_global_var_init.2)  
  
ld: error: undefined symbol: boost::process::v2::shell::~shell()  
>>> referenced by shell.cpp:32  
>>>               ../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/shell.o:(test_shell_parser::test_method())  
>>> referenced by shell.cpp:46  
>>>               ../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/shell.o:(test_shell_parser::test_method())  
>>> referenced by shell.cpp:56  
>>>               ../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/shell.o:(test_shell_parser::test_method())  
>>> referenced 3 more times  
  
ld: error: undefined symbol: boost::process::v2::shell::args() const  
>>> referenced by shell.cpp:52  
>>>               ../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/shell.o:(test_shell_parser::test_method())  
  
ld: error: undefined symbol: boost::process::v2::shell::parse_()  
>>> referenced by shell.hpp:66 (../../../../boost/process/v2/shell.hpp:66)  
>>>               ../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/shell.o:(boost::process::v2::shell::shell(boost::process::v2::basic_cstring_ref<char, std::__1::char_traits<char> >))  
  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::get_shell_category() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::shell::~shell() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::ext::env(int, boost::system::error_code&) [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::detail::ext::native_env_handle_deleter::operator()(char*) const [--no-allow-shlib-undefined]  
clang++: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++"  -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -o "../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/shell" -Wl,--start-group "../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/shell.o" "../../../../bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost/libtest_impl.so.1.87.0" "../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0" "../../../../bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_system.so.1.87.0" "../../../../bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_filesystem.so.1.87.0"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -fPIC -g -m64 -lkvm  
  
...failed clang-linux.link ../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/shell...  
...skipped <p../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>shell.run for lack of <p../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>shell...  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/pid.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/pid  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::get_shell_category() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::shell::~shell() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::ext::env(int, boost::system::error_code&) [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::detail::ext::native_env_handle_deleter::operator()(char*) const [--no-allow-shlib-undefined]  
clang++: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++"  -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -o "../../../../bin.v2/libs/process/test/v2/pid.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/pid" -Wl,--start-group "../../../../bin.v2/libs/process/test/v2/pid.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/pid.o" "../../../../bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost/libtest_impl.so.1.87.0" "../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0" "../../../../bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_system.so.1.87.0" "../../../../bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_filesystem.so.1.87.0"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -fPIC -g -m64 -lkvm  
  
...failed clang-linux.link ../../../../bin.v2/libs/process/test/v2/pid.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/pid...  
...skipped <p../../../../bin.v2/libs/process/test/v2/pid.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>pid.run for lack of <p../../../../bin.v2/libs/process/test/v2/pid.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>pid...  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/process.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/process  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::get_shell_category() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::shell::~shell() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::ext::env(int, boost::system::error_code&) [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::detail::ext::native_env_handle_deleter::operator()(char*) const [--no-allow-shlib-undefined]  
clang++: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++"  -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -o "../../../../bin.v2/libs/process/test/v2/process.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/process" -Wl,--start-group "../../../../bin.v2/libs/process/test/v2/process.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/process.o" "../../../../bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost/libtest_impl.so.1.87.0" "../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0" "../../../../bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_system.so.1.87.0" "../../../../bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_filesystem.so.1.87.0"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -fPIC -g -m64 -lkvm  
  
...failed clang-linux.link ../../../../bin.v2/libs/process/test/v2/process.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/process...  
...skipped <p../../../../bin.v2/libs/process/test/v2/process.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>process.run for lack of <p../../../../bin.v2/libs/process/test/v2/process.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>process...  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/ext  
ld: error: undefined symbol: boost::process::v2::get_shell_category()  
>>> referenced by shell.hpp:21 (../../../../boost/process/v2/shell.hpp:21)  
>>>               ../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/ext.o:(__cxx_global_var_init.8)  
  
ld: error: undefined symbol: boost::process::v2::shell::~shell()  
>>> referenced by ext.cpp:64  
>>>               ../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/ext.o:(ext::cmd::test_method())  
>>> referenced by ext.cpp:64  
>>>               ../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/ext.o:(ext::cmd::test_method())  
>>> referenced by ext.cpp:90  
>>>               ../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/ext.o:(ext::cmd_exe::test_method())  
>>> referenced 1 more times  
  
ld: error: undefined symbol: boost::process::v2::detail::ext::find_end(char*)  
>>> referenced by env.hpp:100 (../../../../boost/process/v2/ext/env.hpp:100)  
>>>               ../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/ext.o:(boost::process::v2::ext::env_view::end() const)  
  
ld: error: undefined symbol: boost::process::v2::detail::ext::dereference(char*)  
>>> referenced by env.hpp:89 (../../../../boost/process/v2/ext/env.hpp:89)  
>>>               ../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/ext.o:(boost::process::v2::ext::env_view::iterator::operator*() const)  
  
ld: error: undefined symbol: boost::process::v2::detail::ext::native_env_handle_deleter::operator()(char*) const  
>>> referenced by unique_ptr.h:318 (/usr/include/c++/v1/__memory/unique_ptr.h:318)  
>>>               ../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/ext.o:(std::__1::unique_ptr<char, boost::process::v2::detail::ext::native_env_handle_deleter>::reset(char*))  
  
ld: error: undefined symbol: boost::process::v2::detail::ext::next(char*)  
>>> referenced by env.hpp:77 (../../../../boost/process/v2/ext/env.hpp:77)  
>>>               ../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/ext.o:(boost::process::v2::ext::env_view::iterator::operator++())  
  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::get_shell_category() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::shell::~shell() [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::ext::env(int, boost::system::error_code&) [--no-allow-shlib-undefined]  
ld: error: ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0: undefined reference to boost::process::v2::detail::ext::native_env_handle_deleter::operator()(char*) const [--no-allow-shlib-undefined]  
clang++: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++"  -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost" -Wl,-rpath-link -Wl,"/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden" -o "../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/ext" -Wl,--start-group "../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/ext.o" "../../../../bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost/libtest_impl.so.1.87.0" "../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/libboost_process.so.1.87.0" "../../../../bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_system.so.1.87.0" "../../../../bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-hidden/libboost_filesystem.so.1.87.0"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -fPIC -g -m64 -lkvm  
  
...failed clang-linux.link ../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/ext...  
...skipped <p../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>ext.run for lack of <p../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>ext...  
  
...updated 3 targets...  
  
...skipped 14 targets...  
   <p../../../../bin.v2/libs/process/test/v2/cstring_ref.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>cstring_ref.run  
   <p../../../../bin.v2/libs/process/test/v2/cstring_ref.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>cstring_ref.test  
   <p../../../../bin.v2/libs/process/test/v2/environment.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>environment.run  
   <p../../../../bin.v2/libs/process/test/v2/environment.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>environment.test  
   <p../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>ext.run  
   <p../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>ext.test  
   <p../../../../bin.v2/libs/process/test/v2/pid.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>pid.run  
   <p../../../../bin.v2/libs/process/test/v2/pid.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>pid.test  
   <p../../../../bin.v2/libs/process/test/v2/process.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>process.run  
   <p../../../../bin.v2/libs/process/test/v2/process.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>process.test  
   <p../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>shell.run  
   <p../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>shell.test  
   <p../../../../bin.v2/libs/process/test/v2/utf8.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>utf8.run  
   <p../../../../bin.v2/libs/process/test/v2/utf8.test/clang-linux-13/debug/x86_64/boost.process.fs-boost>utf8.test  
  
...failed updating 8 targets...  
   clang-linux.link ../../../../bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost/target  
   clang-linux.link ../../../../bin.v2/libs/process/test/v2/cstring_ref.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/cstring_ref  
   clang-linux.link ../../../../bin.v2/libs/process/test/v2/environment.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/environment  
   clang-linux.link ../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/ext  
   clang-linux.link ../../../../bin.v2/libs/process/test/v2/pid.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/pid  
   clang-linux.link ../../../../bin.v2/libs/process/test/v2/process.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/process  
   clang-linux.link ../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/shell  
   clang-linux.link ../../../../bin.v2/libs/process/test/v2/utf8.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/utf8  
openbsd$   
```

---
