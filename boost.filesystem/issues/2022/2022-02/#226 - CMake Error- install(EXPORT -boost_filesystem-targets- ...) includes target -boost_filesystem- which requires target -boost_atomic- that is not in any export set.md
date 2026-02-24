# #226 - CMake Error: install(EXPORT "boost_filesystem-targets" ...) includes target "boost_filesystem" which requires target "boost_atomic" that is not in any export set [Closed]

> Username: mloskot  
> Created at: 2022-02-02 10:38:34 UTC  
> Updated at: 2022-02-02 11:43:24 UTC  
> Closed at: 2022-02-02 11:31:02 UTC  
> Url: https://github.com/boostorg/filesystem/issues/226  

I'm using the Boost superproject from the current `develop` branch and running CMake  (via build scripts from https://github.com/boostorg/boost-ci):  
  
```  
cmake -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Debug -DBOOST_INCLUDE_LIBRARIES=gil -DBUILD_SHARED_LIBS=ON -DBUILD_TESTING=ON -DBoost_VERBOSE=ON ..  
```  
  
which fails this way:  
  
```  
-- The CXX compiler identification is GNU 10.3.0  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Boost: using system layout: include, bin, lib, lib/cmake  
-- Boost: Debug build, shared libraries, MPI OFF, Python OFF, testing ON  
-- Boost: libraries included: gil  
-- Adding Boost dependency assert  
-- Looking for C++ include pthread.h  
-- Looking for C++ include pthread.h - found  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Failed  
-- Check if compiler accepts -pthread  
-- Check if compiler accepts -pthread - yes  
-- Found Threads: TRUE  
-- Performing Test BOOST_ATOMIC_TARGET_X86  
-- Performing Test BOOST_ATOMIC_TARGET_X86 - Success  
-- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE2  
-- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE2 - Success  
-- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE41  
-- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE41 - Success  
-- Adding Boost dependency concept_check  
-- Adding Boost dependency config  
-- The C compiler identification is GNU 10.3.0  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Check for working C compiler: /usr/bin/cc - skipped  
-- Detecting C compile features  
-- Detecting C compile features - done  
-- Adding Boost dependency container_hash  
-- Boost.Context: architecture x86_64, binary format elf, ABI sysv, assembler gas, suffix .S, implementation fcontext  
-- The ASM compiler identification is GNU  
-- Found assembler: /usr/bin/cc  
-- Adding Boost dependency conversion  
-- Adding Boost dependency core  
-- Adding Boost dependency detail  
-- Boost.Fiber: NUMA target OS is linux  
-- Adding Boost dependency filesystem  
-- Performing Test BOOST_FILESYSTEM_HAS_INIT_PRIORITY  
-- Performing Test BOOST_FILESYSTEM_HAS_INIT_PRIORITY - Success  
-- Performing Test BOOST_FILESYSTEM_HAS_CXX20_ATOMIC_REF  
-- Performing Test BOOST_FILESYSTEM_HAS_CXX20_ATOMIC_REF - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BLKSIZE  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BLKSIZE - Success  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIM  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIM - Success  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMENSEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMENSEC - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMENSEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMENSEC - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STATX  
-- Performing Test BOOST_FILESYSTEM_HAS_STATX - Success  
-- Performing Test BOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW  
-- Performing Test BOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW - Success  
-- Adding Boost dependency function_types  
-- Adding Boost dependency fusion  
-- Adding Boost dependency integer  
-- Adding Boost dependency io  
-- Found ZLIB: /usr/lib/x86_64-linux-gnu/libz.so (found version "1.2.11")  
-- Found LibLZMA: /usr/lib/x86_64-linux-gnu/liblzma.so (found version "5.2.5")  
-- Performing Test BOOST_IOSTREAMS_HAS_LZMA_CPUTHREADS  
-- Performing Test BOOST_IOSTREAMS_HAS_LZMA_CPUTHREADS - Success  
-- Boost.Iostreams: ZLIB ON, BZip2 OFF, LZMA ON (multithreaded), Zstd OFF  
-- Adding Boost dependency iterator  
-- Performing Test Iconv_IS_BUILT_IN  
-- Performing Test Iconv_IS_BUILT_IN - Success  
-- Found Iconv: /usr/lib/x86_64-linux-gnu/libc.so  
-- Boost.Locale: iconv ON, ICU OFF, POSIX ON, std ON, winapi OFF  
-- Performing Test BOOST_LOG_TARGET_X86  
-- Performing Test BOOST_LOG_TARGET_X86 - Success  
-- Performing Test BOOST_LOG_HAS_LOCK_FREE_ATOMIC_INT32  
-- Performing Test BOOST_LOG_HAS_LOCK_FREE_ATOMIC_INT32 - Failed  
-- Performing Test BOOST_LOG_COMPILER_HAS_SSSE3  
-- Performing Test BOOST_LOG_COMPILER_HAS_SSSE3 - Success  
-- Performing Test BOOST_LOG_COMPILER_HAS_AVX2  
-- Performing Test BOOST_LOG_COMPILER_HAS_AVX2 - Success  
-- Performing Test BOOST_LOG_HAS_NATIVE_SYSLOG  
-- Performing Test BOOST_LOG_HAS_NATIVE_SYSLOG - Success  
-- Boost.Math: standalone mode OFF  
-- Adding Boost dependency move  
-- Adding Boost dependency mp11  
-- Adding Boost dependency mpl  
-- Boost.Multiprecision: standalone mode OFF  
-- Performing Test BOOST_NOWIDE_HAS_LFS  
-- Performing Test BOOST_NOWIDE_HAS_LFS - Success  
-- Performing Test BOOST_NOWIDE_HAS_INIT_PRIORITY  
-- Performing Test BOOST_NOWIDE_HAS_INIT_PRIORITY - Success  
-- Adding Boost dependency optional  
-- Adding Boost dependency predef  
-- Adding Boost dependency preprocessor  
-- Adding Boost dependency smart_ptr  
-- Performing Test BOOST_STACKTRACE_HAS_BACKTRACE  
-- Performing Test BOOST_STACKTRACE_HAS_BACKTRACE - Success  
-- Performing Test BOOST_STACKTRACE_HAS_WINDBG  
-- Performing Test BOOST_STACKTRACE_HAS_WINDBG - Failed  
-- Performing Test BOOST_STACKTRACE_HAS_WINDBG_CACHED  
-- Performing Test BOOST_STACKTRACE_HAS_WINDBG_CACHED - Failed  
-- Boost.Stacktrace: noop ON, backtrace ON, addr2line ON, basic ON, windbg OFF, windbg_cached OFF  
-- Adding Boost dependency static_assert  
-- Adding Boost dependency system  
-- Boost.Thread: threading API is pthread  
-- Adding Boost dependency throw_exception  
-- Adding Boost dependency tuple  
-- Adding Boost dependency type_traits  
-- Adding Boost dependency typeof  
-- Adding Boost dependency utility  
-- Adding Boost dependency variant2  
-- Adding Boost dependency winapi  
-- Configuring done  
CMake Error: install(EXPORT "boost_filesystem-targets" ...) includes target "boost_filesystem" which requires target "boost_atomic" that is not in any export set.  
-- Generating done  
CMake Generate step failed.  Build files cannot be regenerated correctly.  
```  
  
It looks like some dependencies of Boost.Filesystem can not be resolved.

---

## Comment 1

> Username: Flamefire  
> Created at: 2022-02-02 11:27:27 UTC  
> Url: https://github.com/boostorg/filesystem/issues/226#issuecomment-1027844931  

I think this line is the problem : https://github.com/boostorg/filesystem/blob/41d076ace558cfae01e233f9746dc955dcf78dba/CMakeLists.txt#L197  
  
The dependency scanner by @pdimov reads full lines only. So put the atomic lib in a new line, see the winapi lib below  
  
I can also check if I can enhance the depency scanner to allow this Syntax. Bothered me a while already ;)  
  
Btw: not fully sure how well conditional dependencies work with the generated config files (for consumers find_package)

---

## Comment 2

> Username: Lastique  
> Created at: 2022-02-02 11:31:02 UTC  
> Url: https://github.com/boostorg/filesystem/issues/226#issuecomment-1027847644  

The CMake syntax in Boost.Filesystem is correct. If the dependency scanner does not recognize it then that is where the issue should be fixed, IMHO.

---

## Comment 3

> Username: Lastique  
> Created at: 2022-02-02 11:43:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/226#issuecomment-1027856355  

I've created a Boost.CMake issue here: https://github.com/boostorg/cmake/issues/17.
