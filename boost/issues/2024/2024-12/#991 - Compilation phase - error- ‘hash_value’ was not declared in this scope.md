# #991 - Compilation phase : error: ‘hash_value’ was not declared in this scope [Closed]

> Username: raphael10-collab  
> Created at: 2024-12-16 09:35:47 UTC  
> Updated at: 2025-09-04 09:38:22 UTC  
> Closed at: 2025-09-04 09:38:22 UTC  
> Url: https://github.com/boostorg/boost/issues/991  

raphy@raohy:~$ git clone --recurse-submodules https://github.com/boostorg/boost.git  
      
      
    raphy@raohy:~/boost$ cmake -B builddir  
    -- The CXX compiler identification is GNU 14.2.0  
    -- Detecting CXX compiler ABI info  
    -- Detecting CXX compiler ABI info - done  
    -- Check for working CXX compiler: /usr/bin/c++ - skipped  
    -- Detecting CXX compile features  
    -- Detecting CXX compile features - done  
    -- Boost: using system layout: include, bin, lib, lib/cmake  
    -- Boost: Release build, static libraries, MPI OFF, Python OFF, testing OFF  
    -- Performing Test CMAKE_HAVE_LIBC_PTHREAD  
    -- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success  
    -- Found Threads: TRUE  
    -- Performing Test BOOST_ATOMIC_TARGET_X86  
    -- Performing Test BOOST_ATOMIC_TARGET_X86 - Success  
    -- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE2  
    -- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE2 - Success  
    -- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE41  
    -- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE41 - Success  
    -- Performing Test BOOST_CHARCONV_QUADMATH_FOUND  
    -- Performing Test BOOST_CHARCONV_QUADMATH_FOUND - Success  
    -- Boost.Charconv: quadmath support ON  
    -- The C compiler identification is GNU 12.3.0  
    -- Detecting C compiler ABI info  
    -- Detecting C compiler ABI info - done  
    -- Check for working C compiler: /usr/bin/cc - skipped  
    -- Detecting C compile features  
    -- Detecting C compile features - done  
    -- Boost.Context: architecture x86_64, binary format elf, ABI sysv, assembler gas, suffix .S, implementation fcontext  
    -- The ASM compiler identification is GNU  
    -- Found assembler: /usr/bin/cc  
    -- Boost.Fiber: NUMA target OS is linux  
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
    -- Performing Test BOOST_FILESYSTEM_HAS_DIRENT_D_TYPE  
    -- Performing Test BOOST_FILESYSTEM_HAS_DIRENT_D_TYPE - Success  
    -- Performing Test BOOST_FILESYSTEM_HAS_POSIX_AT_APIS  
    -- Performing Test BOOST_FILESYSTEM_HAS_POSIX_AT_APIS - Success  
    -- Performing Test BOOST_FILESYSTEM_HAS_FALLOCATE  
    -- Performing Test BOOST_FILESYSTEM_HAS_FALLOCATE - Success  
    -- Found ZLIB: /usr/lib/x86_64-linux-gnu/libz.so (found version "1.3")  
    -- Found BZip2: /usr/lib/x86_64-linux-gnu/libbz2.so (found version "1.0.8")  
    -- Found LibLZMA: /usr/lib/x86_64-linux-gnu/liblzma.so (found version "5.4.5")  
    -- Performing Test BOOST_IOSTREAMS_HAS_LZMA_CPUTHREADS  
    -- Performing Test BOOST_IOSTREAMS_HAS_LZMA_CPUTHREADS - Success  
    -- Boost.Iostreams: ZLIB ON, BZip2 ON, LZMA ON (multithreaded), Zstd ON  
    -- Performing Test Iconv_IS_BUILT_IN  
    -- Performing Test Iconv_IS_BUILT_IN - Success  
    -- Found Iconv: built in to C library  
    -- Found the following ICU libraries:  
    --   data (required): /usr/local/lib/libicudata.so  
    --   i18n (required): /usr/local/lib/libicui18n.so  
    --   uc (required): /usr/local/lib/libicuuc.so  
    -- Found ICU: /usr/local/include (found version "72.1")  
    -- Boost.Locale: iconv ON, ICU ON, POSIX ON, std ON, winapi OFF  
    -- Performing Test BOOST_LOG_TARGET_X86  
    -- Performing Test BOOST_LOG_TARGET_X86 - Success  
    -- Performing Test BOOST_LOG_HAS_LOCK_FREE_ATOMIC_INT32  
    -- Performing Test BOOST_LOG_HAS_LOCK_FREE_ATOMIC_INT32 - Success  
    -- Performing Test BOOST_LOG_COMPILER_HAS_SSSE3  
    -- Performing Test BOOST_LOG_COMPILER_HAS_SSSE3 - Success  
    -- Performing Test BOOST_LOG_COMPILER_HAS_AVX2  
    -- Performing Test BOOST_LOG_COMPILER_HAS_AVX2 - Success  
    -- Performing Test BOOST_LOG_HAS_NATIVE_SYSLOG  
    -- Performing Test BOOST_LOG_HAS_NATIVE_SYSLOG - Success  
    -- Boost.Math: standalone mode OFF  
    -- Boost.Multiprecision: standalone mode OFF  
    -- Found OpenSSL: /usr/lib/x86_64-linux-gnu/libcrypto.so (found version "3.0.13")  
    -- Performing Test BOOST_NOWIDE_HAS_LFS  
    -- Performing Test BOOST_NOWIDE_HAS_LFS - Success  
    -- Performing Test BOOST_NOWIDE_HAS_INIT_PRIORITY  
    -- Performing Test BOOST_NOWIDE_HAS_INIT_PRIORITY - Success  
    -- Found the following ICU libraries:  
    --   data (required): /usr/local/lib/libicudata.so  
    --   i18n (required): /usr/local/lib/libicui18n.so  
    --   uc (required): /usr/local/lib/libicuuc.so  
    -- Performing Test BOOST_STACKTRACE_HAS_BACKTRACE  
    -- Performing Test BOOST_STACKTRACE_HAS_BACKTRACE - Success  
    -- Performing Test BOOST_STACKTRACE_HAS_WINDBG  
    -- Performing Test BOOST_STACKTRACE_HAS_WINDBG - Failed  
    -- Performing Test BOOST_STACKTRACE_HAS_WINDBG_CACHED  
    -- Performing Test BOOST_STACKTRACE_HAS_WINDBG_CACHED - Failed  
    -- Boost.Stacktrace: noop ON, backtrace ON, addr2line ON, basic ON, windbg OFF, windbg_cached OFF, from_exception ON  
    -- Boost.Thread: threading API is pthread  
    -- Configuring done (4.5s)  
    -- Generating done (0.2s)  
    -- Build files have been written to: /home/raphy/boost/builddir  
    raphy@raohy:~/boost$  
  
  
  
  
    raphy@raohy:~/boost$ cmake --build builddir/  
    [ 80%] Built target boost_type_erasure  
    [ 80%] Building CXX object libs/url/CMakeFiles/boost_url.dir/src/authority_view.cpp.o  
    In file included from /home/raphy/boost/libs/url/include/boost/url/pct_string_view.hpp:18,  
                     from /home/raphy/boost/libs/url/include/boost/url/authority_view.hpp:17,  
                     from /home/raphy/boost/libs/url/src/authority_view.cpp:12:  
    /home/raphy/boost/libs/url/include/boost/url/grammar/string_view_base.hpp: In function ‘std::size_t boost::urls::grammar::hash_value(const string_view_base&)’:  
    /home/raphy/boost/libs/url/include/boost/url/grammar/string_view_base.hpp:867:16: error: ‘hash_value’ was not declared in this scope  
      867 |         return hash_value(s.s_);  
          |                ^~~~~~~~~~  
    /home/raphy/boost/libs/url/include/boost/url/grammar/string_view_base.hpp:867:16: note: suggested alternatives:  
    In file included from /home/raphy/boost/libs/system/include/boost/system/error_code.hpp:13,  
                     from /home/raphy/boost/libs/url/include/boost/url/error_types.hpp:14,  
                     from /home/raphy/boost/libs/url/include/boost/url/error.hpp:14,  
                     from /home/raphy/boost/libs/url/include/boost/url/ipv4_address.hpp:15,  
                     from /home/raphy/boost/libs/url/include/boost/url/authority_view.hpp:15:  
    /home/raphy/boost/libs/system/include/boost/system/detail/error_code.hpp:618:20: note:   ‘boost::system::hash_value’  
      618 | inline std::size_t hash_value( error_code const & ec )  
          |                    ^~~~~~~~~~  
    In file included from /home/raphy/boost/libs/system/include/boost/system/result.hpp:12,  
                     from /home/raphy/boost/libs/url/include/boost/url/error_types.hpp:16:  
    /home/raphy/boost/libs/variant2/include/boost/variant2/variant.hpp:2391:34: note:   ‘boost::variant2::hash_value’  
     2391 | template<class... T> std::size_t hash_value( variant<T...> const & v )  
          |                                  ^~~~~~~~~~  
    gmake[2]: *** [libs/url/CMakeFiles/boost_url.dir/build.make:76: libs/url/CMakeFiles/boost_url.dir/src/authority_view.cpp.o] Error 1  
    gmake[1]: *** [CMakeFiles/Makefile2:3931: libs/url/CMakeFiles/boost_url.dir/all] Error 2  
    gmake: *** [Makefile:156: all] Error 2  
      
  
OS: Ubuntu 24.04  
gcc :  14.2.0

---

## Comment 1

> Username: owacoder  
> Created at: 2024-12-17 21:49:42 UTC  
> Url: https://github.com/boostorg/boost/issues/991#issuecomment-2549712100  

Looks like `#include <boost/container_hash/hash.hpp>` inside `string_view_base.hpp` fixes the issue.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-12-19 14:53:09 UTC  
> Url: https://github.com/boostorg/boost/issues/991#issuecomment-2554433307  

Should be fixed.

---

## Comment 3

> Username: seektechnz  
> Created at: 2024-12-20 00:07:55 UTC  
> Url: https://github.com/boostorg/boost/issues/991#issuecomment-2555989371  

Seems to be working fine now as mentioned in boostorg/url#892.

---

## Comment 4

> Username: nigels-com  
> Created at: 2025-09-04 05:50:53 UTC  
> Url: https://github.com/boostorg/boost/issues/991#issuecomment-3252013442  

Can this issue be closed now?  
Having no problem here with this building with cmake, for boost 1.89.0.
