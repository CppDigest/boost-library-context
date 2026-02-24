# #415 bp2::ext::env() Fixes [Merged]

> Username: ghost  
> Created at: 2024-10-29 01:37:31 UTC  
> Updated at: 2024-10-29 20:39:50 UTC  
> Merged at: 2024-10-29 15:06:21 UTC  
> Closed at: 2024-10-29 15:06:21 UTC  
> Url: https://github.com/boostorg/process/pull/415  

Adds complete DragonFly BSD, NetBSD, and SunOS support to bp2::ext::env(). Also makes FreeBSD use libkvm API's for bp2::ext::env(), giving it the same exact code as DragonFly BSD besides the required headers, removing dependency on libprocstat. Removes FreeBSD's need for libutil and replaces it with libkvm. Now FreeBSD no longer needs libprocstat or libutil; the libkvm, libpthread, and libc libraries are the only dependencies, and those are the same exact dependencies all the other *BSD's as well as SunOS rely on, unifying the dependencies to be the same set of libraries on those platforms.  
  
clang build:  
```  
freebsd@freebsd:~/boost/libs/process/test/v2 % ../../../../b2 visibility=global  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
    - symlinks supported       : yes (cached)  
    - has -Wl,--no-undefined   : yes (cached) [2]  
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
    - has stat::st_birthtim    : yes (cached) [2]  
    - has stat::st_birthtimensec : yes (cached) [2]  
    - has stat::st_birthtimespec : yes (cached) [2]  
    - has fdopendir(O_NOFOLLOW) : yes (cached) [2]  
    - has dirent::d_type       : yes (cached) [2]  
    - has POSIX *at APIs       : no  (cached) [2]  
    - has fallocate            : no  (cached) [2]  
    - BOOST_COMP_GNUC >= 4.3.0 : no  (cached) [2]  
  
[1] clang-18  
[2] clang-linux-18/debug/x86_64/boost.process.fs-boost/visibility-global  
...patience...  
...patience...  
...found 3573 targets...  
freebsd@freebsd:~/boost/libs/process/test/v2 %   
```  
gcc build:  
```  
freebsd@freebsd:~/boost/libs/process/test/v2 % ../../../../b2 toolset=gcc-13 visibility=global  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
    - symlinks supported       : yes (cached)  
    - has -Wl,--no-undefined   : yes (cached) [2]  
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
    - has stat::st_birthtim    : yes (cached) [2]  
    - has stat::st_birthtimensec : yes (cached) [2]  
    - has stat::st_birthtimespec : yes (cached) [2]  
    - has fdopendir(O_NOFOLLOW) : yes (cached) [2]  
    - has dirent::d_type       : yes (cached) [2]  
    - has POSIX *at APIs       : no  (cached) [2]  
    - has fallocate            : no  (cached) [2]  
    - BOOST_COMP_GNUC >= 4.3.0 : yes (cached) [2]  
  
[1] gcc-13  
[2] gcc-13/debug/x86_64/boost.process.fs-boost/visibility-global  
...patience...  
...patience...  
...found 3573 targets...  
freebsd@freebsd:~/boost/libs/process/test/v2 %   
```

---
