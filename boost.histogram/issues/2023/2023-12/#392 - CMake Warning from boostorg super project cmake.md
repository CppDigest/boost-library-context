# #392 - CMake Warning from boostorg super project cmake [Closed]

> Username: mborland  
> Created at: 2023-12-12 11:54:33 UTC  
> Updated at: 2024-04-23 12:11:47 UTC  
> Closed at: 2024-04-23 12:11:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/392  

When running cmake configuration from boost root the following warning is issued:  
  
```  
CMake Warning (dev) at libs/histogram/test/CMakeLists.txt:5 (find_package):  
  Policy CMP0148 is not set: The FindPythonInterp and FindPythonLibs modules  
  are removed.  Run "cmake --help-policy CMP0148" for policy details.  Use  
  the cmake_policy command to set the policy and suppress this warning.  
```  
  
Full build log:  
```  
-- The CXX compiler identification is AppleClang 15.0.0.15000100  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Boost: using system layout: include, bin, lib, lib/cmake  
-- Boost: Debug build, static libraries, MPI OFF, Python OFF, testing 1  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success  
-- Found Threads: TRUE    
-- Performing Test BOOST_ATOMIC_TARGET_X86  
-- Performing Test BOOST_ATOMIC_TARGET_X86 - Failed  
-- The C compiler identification is AppleClang 15.0.0.15000100  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc - skipped  
-- Detecting C compile features  
-- Detecting C compile features - done  
-- Boost.Context: architecture arm64, binary format mach-o, ABI aapcs, assembler gas, suffix .S, implementation fcontext  
-- The ASM compiler identification is AppleClang  
-- Found assembler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc  
-- Boost.Fiber: NUMA target OS is none  
-- Performing Test BOOST_FILESYSTEM_HAS_INIT_PRIORITY  
-- Performing Test BOOST_FILESYSTEM_HAS_INIT_PRIORITY - Success  
-- Performing Test BOOST_FILESYSTEM_HAS_CXX20_ATOMIC_REF  
-- Performing Test BOOST_FILESYSTEM_HAS_CXX20_ATOMIC_REF - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BLKSIZE  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BLKSIZE - Success  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIM  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIM - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMENSEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMENSEC - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC - Success  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMENSEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMENSEC - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC - Success  
-- Performing Test BOOST_FILESYSTEM_HAS_STATX  
-- Performing Test BOOST_FILESYSTEM_HAS_STATX - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STATX_SYSCALL  
-- Performing Test BOOST_FILESYSTEM_HAS_STATX_SYSCALL - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW  
-- Performing Test BOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW - Success  
-- Performing Test BOOST_FILESYSTEM_HAS_DIRENT_D_TYPE  
-- Performing Test BOOST_FILESYSTEM_HAS_DIRENT_D_TYPE - Success  
-- Performing Test BOOST_FILESYSTEM_HAS_POSIX_AT_APIS  
-- Performing Test BOOST_FILESYSTEM_HAS_POSIX_AT_APIS - Failed  
CMake Warning (dev) at libs/histogram/test/CMakeLists.txt:5 (find_package):  
  Policy CMP0148 is not set: The FindPythonInterp and FindPythonLibs modules  
  are removed.  Run "cmake --help-policy CMP0148" for policy details.  Use  
  the cmake_policy command to set the policy and suppress this warning.  
  
This warning is for project developers.  Use -Wno-dev to suppress it.  
  
-- Found PythonInterp: /opt/homebrew/bin/python3.12 (found version "3.12.1")   
-- Found ZLIB: /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX14.2.sdk/usr/lib/libz.tbd (found version "1.2.12")    
-- Found BZip2: /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX14.2.sdk/usr/lib/libbz2.tbd (found version "1.0.8")   
-- Found LibLZMA: /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX14.2.sdk/usr/lib/liblzma.tbd (found version "5.4.5")   
-- Performing Test BOOST_IOSTREAMS_HAS_LZMA_CPUTHREADS  
-- Performing Test BOOST_IOSTREAMS_HAS_LZMA_CPUTHREADS - Failed  
-- Boost.Iostreams: ZLIB ON, BZip2 ON, LZMA ON, Zstd ON  
-- Performing Test Iconv_IS_BUILT_IN  
-- Performing Test Iconv_IS_BUILT_IN - Failed  
-- Found Iconv: /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX14.2.sdk/usr/lib/libiconv.tbd    
-- Boost.Locale: iconv ON, ICU OFF, POSIX ON, std ON, winapi OFF  
-- Performing Test BOOST_LOG_TARGET_X86  
-- Performing Test BOOST_LOG_TARGET_X86 - Failed  
-- Performing Test BOOST_LOG_HAS_LOCK_FREE_ATOMIC_INT32  
-- Performing Test BOOST_LOG_HAS_LOCK_FREE_ATOMIC_INT32 - Success  
-- Performing Test BOOST_LOG_HAS_NATIVE_SYSLOG  
-- Performing Test BOOST_LOG_HAS_NATIVE_SYSLOG - Success  
-- Boost.Math: standalone mode OFF  
-- Boost.Multiprecision: standalone mode OFF  
-- Found OpenSSL: /opt/homebrew/Cellar/openssl@3/3.2.0_1/lib/libcrypto.dylib (found version "3.2.0")    
-- Performing Test BOOST_NOWIDE_HAS_LFS  
-- Performing Test BOOST_NOWIDE_HAS_LFS - Success  
-- Performing Test BOOST_NOWIDE_HAS_INIT_PRIORITY  
-- Performing Test BOOST_NOWIDE_HAS_INIT_PRIORITY - Success  
-- Performing Test _BOOST_NOWIDE_SUGGEST_OVERRIDE_SUPPORTED  
-- Performing Test _BOOST_NOWIDE_SUGGEST_OVERRIDE_SUPPORTED - Success  
CMake Deprecation Warning at libs/predef/CMakeLists.txt:23 (cmake_minimum_required):  
  Compatibility with CMake < 3.5 will be removed from a future version of  
  CMake.  
  
  Update the VERSION argument <min> value or use a ...<max> suffix to tell  
  CMake that the project does not need compatibility with older versions.  
  
  
-- ptree_test boost_property_tree-test-multi SRCS test_multi_module1.cpp;test_multi_module2.cpp  
-- ptree_test boost_property_tree-test-property-tree SRCS test_property_tree.cpp;test_property_tree.hpp  
-- ptree_test boost_property_tree-test-info-parser SRCS test_info_parser.cpp  
-- ptree_test boost_property_tree-test-rapidxml SRCS test_rapidxml.cpp  
-- ptree_test boost_property_tree-test-json-parser SRCS test_json_parser.cpp  
-- ptree_test boost_property_tree-test-json-parser2 SRCS test_json_parser2.cpp  
-- ptree_test boost_property_tree-test-ini-parser SRCS test_ini_parser.cpp  
-- ptree_test boost_property_tree-test-xml-parser-rapidxml SRCS test_xml_parser_rapidxml.cpp  
-- [property_tree] superproject build - skipping examples  
-- Performing Test BOOST_STACKTRACE_HAS_BACKTRACE  
-- Performing Test BOOST_STACKTRACE_HAS_BACKTRACE - Failed  
-- Performing Test BOOST_STACKTRACE_HAS_WINDBG  
-- Performing Test BOOST_STACKTRACE_HAS_WINDBG - Failed  
-- Performing Test BOOST_STACKTRACE_HAS_WINDBG_CACHED  
-- Performing Test BOOST_STACKTRACE_HAS_WINDBG_CACHED - Failed  
-- Boost.Stacktrace: noop ON, backtrace OFF, addr2line ON, basic ON, windbg OFF, windbg_cached OFF  
-- Boost.Thread: threading API is pthread  
-- Configuring done (5.3s)  
-- Generating done (1.2s)  
```
