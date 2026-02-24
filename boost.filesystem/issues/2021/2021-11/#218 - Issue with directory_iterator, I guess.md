# #218 - Issue with directory_iterator, I guess? [Closed]

> Username: amirmasoudabdol  
> Created at: 2021-11-26 10:59:28 UTC  
> Updated at: 2021-11-26 16:03:24 UTC  
> Closed at: 2021-11-26 12:31:38 UTC  
> Url: https://github.com/boostorg/filesystem/issues/218  

I'm trying to build Boost 1.78.0-Beta1 on a M1 Mac, on macOS Monterey, and I use CMake to achieve this. I attached the CMake config, so that you can see the config. The problem is that, I guess, everywhere in my code that I use `directory_iterator`, the compilation/linking fails, and I don't know why. I can see that some of the configuration are failing during the configuration but I don't know why, or whether they are the culprit.   
  
#### CMake Config  
  
```  
-- The CXX compiler identification is AppleClang 13.0.0.13000029  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Boost: using system layout: include, bin, lib, lib/cmake  
-- Boost: Release build, static libraries, MPI OFF, Python OFF, testing OFF  
-- Looking for C++ include pthread.h  
-- Looking for C++ include pthread.h - found  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success  
-- Found Threads: TRUE  
-- Performing Test BOOST_ATOMIC_TARGET_X86  
-- Performing Test BOOST_ATOMIC_TARGET_X86 - Failed  
-- The C compiler identification is AppleClang 13.0.0.13000029  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc - skipped  
-- Detecting C compile features  
-- Detecting C compile features - done  
-- Boost.Context: architecture arm64, binary format mach-o, ABI aapcs, assembler gas, suffix .S, implementation fcontext  
-- The ASM compiler identification is Clang with GNU-like command-line  
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
-- Found ZLIB: /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.0.sdk/usr/lib/libz.tbd (found version "1.2.11")  
-- Found BZip2: /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.0.sdk/usr/lib/libbz2.tbd (found version "1.0.8")  
-- Found LibLZMA: /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.0.sdk/usr/lib/liblzma.tbd (found version "5.2.5")  
-- Performing Test BOOST_IOSTREAMS_HAS_LZMA_CPUTHREADS  
-- Performing Test BOOST_IOSTREAMS_HAS_LZMA_CPUTHREADS - Failed  
-- Boost.Iostreams: ZLIB ON, BZip2 ON, LZMA ON, Zstd ON  
-- Performing Test Iconv_IS_BUILT_IN  
-- Performing Test Iconv_IS_BUILT_IN - Failed  
-- Found Iconv: /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.0.sdk/usr/lib/libiconv.tbd (found version "1.11")  
-- Boost.Locale: iconv ON, ICU OFF, POSIX ON, std ON, winapi OFF  
-- Performing Test BOOST_LOG_TARGET_X86  
-- Performing Test BOOST_LOG_TARGET_X86 - Failed  
-- Performing Test BOOST_LOG_HAS_LOCK_FREE_ATOMIC_INT32  
-- Performing Test BOOST_LOG_HAS_LOCK_FREE_ATOMIC_INT32 - Success  
-- Performing Test BOOST_LOG_HAS_NATIVE_SYSLOG  
-- Performing Test BOOST_LOG_HAS_NATIVE_SYSLOG - Success  
-- Boost.Math: standalone mode OFF  
-- Performing Test BOOST_NOWIDE_HAS_LFS  
-- Performing Test BOOST_NOWIDE_HAS_LFS - Failed  
-- Performing Test BOOST_NOWIDE_HAS_INIT_PRIORITY  
-- Performing Test BOOST_NOWIDE_HAS_INIT_PRIORITY - Success  
-- Performing Test BOOST_STACKTRACE_HAS_BACKTRACE  
-- Performing Test BOOST_STACKTRACE_HAS_BACKTRACE - Failed  
-- Performing Test BOOST_STACKTRACE_HAS_WINDBG  
-- Performing Test BOOST_STACKTRACE_HAS_WINDBG - Failed  
-- Performing Test BOOST_STACKTRACE_HAS_WINDBG_CACHED  
-- Performing Test BOOST_STACKTRACE_HAS_WINDBG_CACHED - Failed  
-- Boost.Stacktrace: noop ON, backtrace OFF, addr2line ON, basic ON, windbg OFF, windbg_cached OFF  
-- Boost.Thread: threading API is pthread  
-- Configuring done  
-- Generating done  
-- Build files have been written to: /Users/amabdol/Developer/GitHub/boost/build  
```  
  
#### Build  
```  
Undefined symbols for architecture arm64:  
  "boost::filesystem::path::m_path_iterator_increment(boost::filesystem::path::iterator&)", referenced from:  
      boost::filesystem::path::iterator::increment() in libCommon.a(tempfiles.cpp.o)  
  "boost::filesystem::detail::remove_all(boost::filesystem::path const&, boost::system::error_code*)", referenced from:  
      boost::filesystem::remove_all(boost::filesystem::path const&) in otoolstuff.cpp.o  
      boost::filesystem::remove_all(boost::filesystem::path const&, boost::system::error_code&) in libCommon.a(tempfiles.cpp.o)  
      boost::filesystem::remove_all(boost::filesystem::path const&) in libCommon.a(tempfiles.cpp.o)  
  "boost::filesystem::detail::dir_itr_close(void*&, void*&)", referenced from:  
      boost::filesystem::detail::dir_itr_imp::~dir_itr_imp() in libCommon.a(tempfiles.cpp.o)  
  "boost::filesystem::detail::create_directories(boost::filesystem::path const&, boost::system::error_code*)", referenced from:  
      boost::filesystem::create_directories(boost::filesystem::path const&, boost::system::error_code&) in libCommon.a(tempfiles.cpp.o)  
      boost::filesystem::create_directories(boost::filesystem::path const&, boost::system::error_code&) in libCommon.a(dirs.cpp.o)  
  "boost::filesystem::detail::directory_iterator_construct(boost::filesystem::directory_iterator&, boost::filesystem::path const&, unsigned int, boost::system::error_code*)", referenced from:  
      boost::filesystem::directory_iterator::directory_iterator(boost::filesystem::path const&, boost::filesystem::directory_options) in libCommon.a(tempfiles.cpp.o)  
      boost::filesystem::directory_iterator::directory_iterator(boost::filesystem::path const&, boost::system::error_code&) in libCommon.a(tempfiles.cpp.o)  
  "boost::filesystem::detail::directory_iterator_increment(boost::filesystem::directory_iterator&, boost::system::error_code*)", referenced from:  
      boost::filesystem::directory_iterator::increment() in libCommon.a(tempfiles.cpp.o)  
  "boost::filesystem::detail::remove(boost::filesystem::path const&, boost::system::error_code*)", referenced from:  
      boost::filesystem::remove(boost::filesystem::path const&, boost::system::error_code&) in libCommon.a(utils.cpp.o)  
      boost::filesystem::remove(boost::filesystem::path const&, boost::system::error_code&) in libCommon.a(tempfiles.cpp.o)  
  "boost::filesystem::detail::rename(boost::filesystem::path const&, boost::filesystem::path const&, boost::system::error_code*)", referenced from:  
      boost::filesystem::rename(boost::filesystem::path const&, boost::filesystem::path const&, boost::system::error_code&) in libCommon.a(utils.cpp.o)  
  "boost::filesystem::detail::status(boost::filesystem::path const&, boost::system::error_code*)", referenced from:  
      boost::filesystem::exists(boost::filesystem::path const&) in otoolstuff.cpp.o  
      boost::filesystem::exists(boost::filesystem::path const&, boost::system::error_code&) in libCommon.a(tempfiles.cpp.o)  
      boost::filesystem::is_directory(boost::filesystem::path const&, boost::system::error_code&) in libCommon.a(tempfiles.cpp.o)  
      boost::filesystem::exists(boost::filesystem::path const&) in libCommon.a(tempfiles.cpp.o)  
      boost::filesystem::exists(boost::filesystem::path const&) in libCommon.a(dirs.cpp.o)  
  "boost::filesystem::detail::file_size(boost::filesystem::path const&, boost::system::error_code*)", referenced from:  
      boost::filesystem::file_size(boost::filesystem::path const&, boost::system::error_code&) in libCommon.a(utils.cpp.o)  
  "boost::filesystem::directory_entry::get_status(boost::system::error_code*) const", referenced from:  
      boost::filesystem::directory_entry::status() const in libCommon.a(tempfiles.cpp.o)  
  "boost::filesystem::path::parent_path() const", referenced from:  
      TempFiles::createSpecific_clipboard(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&) in libCommon.a(tempfiles.cpp.o)  
  "boost::filesystem::path::end() const", referenced from:  
      TempFiles::clearSessionDir() in libCommon.a(tempfiles.cpp.o)  
  "boost::filesystem::path::begin() const", referenced from:  
      TempFiles::clearSessionDir() in libCommon.a(tempfiles.cpp.o)  
  "boost::filesystem::path::compare(boost::filesystem::path const&) const", referenced from:  
      TempFiles::deleteOrphans() in libCommon.a(tempfiles.cpp.o)  
      boost::filesystem::path::compare(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&) const in libCommon.a(tempfiles.cpp.o)  
  "boost::filesystem::path::filename() const", referenced from:  
      TempFiles::deleteOrphans() in libCommon.a(tempfiles.cpp.o)  
ld: symbol(s) not found for architecture arm64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
make[3]: *** [Engine/JASPEngine] Error 1  
make[2]: *** [Engine/CMakeFiles/JASPEngine.dir/all] Error 2  
make[1]: *** [Engine/CMakeFiles/JASPEngine.dir/rule] Error 2  
make: *** [JASPEngine] Error 2  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2021-11-26 12:31:37 UTC  
> Url: https://github.com/boostorg/filesystem/issues/218#issuecomment-979945291  

It looks like your `libCommon.a` target doesn't link against Boost.Filesystem. Check if you have `target_link_libraries(Common PRIVATE Boost::filesystem)` or something similar in your CMakeLists.txt.

---

## Comment 2

> Username: amirmasoudabdol  
> Created at: 2021-11-26 13:10:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/218#issuecomment-979968067  

That is not the case, and even after adding it to the `libCommon.a`, and every other targets that I could think of I still cannot get the build, and get the same error. The curious thing is, if I install the Boost from Homebrew (which comes prebuilt using `clang`), then I don't have this problem (even without overlooking `Boost::filesystem`). That's why I thought something else is probably off. So, I thought there should be something special about the build with Apple Clang that causes this. I looked around a bit and I think it should be something related to this, https://github.com/boostorg/filesystem/issues/147, but the workaround doesn't work for me.

---

## Comment 3

> Username: Lastique  
> Created at: 2021-11-26 14:44:38 UTC  
> Updated at: 2021-11-26 14:59:34 UTC  
> Url: https://github.com/boostorg/filesystem/issues/218#issuecomment-980032026  

Can you verify that `-lboost_filesystem` is actually passed to the compiler? And that it comes after `-lCommon` in the command line?  
  
If that doesn't help then my only guess is that there's something wrong with Boost.Filesystem library you're linking with. Maybe it is built for a different architecture? Or maybe it is somehow damaged? Check the symbols it provides and cross-reference with those you see in the linker errors.  
  
Also check that Boost.Filesystem library you're linking with corresponds to the headers you're using (i.e. these should be from the same Boost release version). Check your library paths (`-L` switches in the linker command lines). If you're linking to a shared library, see if `libboost_filesystem.dylib` is a symlink and that it references to the correct versioned shared library.  
  
Also, if you're switching between Boost versions, make sure you rebuild your code from scratch.

---

## Comment 4

> Username: amirmasoudabdol  
> Created at: 2021-11-26 16:03:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/218#issuecomment-980078175  

After all, you are right. CMake fails to properly link Boost to my project. This was extra confusing to me because `find_package` does not tell me that `${Boost_LIBRARIES}` and other variables are empty, despite telling me that it actually found Boost. So, I was confused, if it is there, then it should be linked, and CMake was telling me that it is, but then was not able to link them.   
  
This is probably caused because I'm trying to pull Boost to my project using the [CPM](https://github.com/cpm-cmake/CPM.cmake), as I described here, https://github.com/cpm-cmake/CPM.cmake/discussions/300.   
  
Thanks again for your help! :-)
