# #185 - Memsan reports use-of-uninitialized-value in 1.76.0 temp_directory_path() [Closed]

> Username: tonyelewis  
> Created at: 2021-04-28 14:00:43 UTC  
> Updated at: 2021-06-04 11:01:53 UTC  
> Closed at: 2021-05-16 20:35:45 UTC  
> Url: https://github.com/boostorg/filesystem/issues/185  

Compiling and executing the following code with Boost 1.75, Clang 10.0 and  `-fsanitize=memory -fsanitize-memory-track-origins -fno-omit-frame-pointer`:  
  
~~~cpp  
#include <boost/filesystem.hpp>  
  
int main() {  
	::boost::filesystem::temp_directory_path();  
}  
~~~  
  
&hellip;gives:  
  
~~~no-highlight  
==8==WARNING: MemorySanitizer: use-of-uninitialized-value  
    #0 0x4d1fd6 in boost::filesystem::detail::status(boost::filesystem::path const&, boost::system::error_code*) /root/.conan/data/boost/1.75.0/_/_/source/source_subfolder/libs/filesystem/src/operations.cpp:2603:7  
    #1 0x4ed91a in boost::filesystem::detail::temp_directory_path(boost::system::error_code*) /root/.conan/data/boost/1.75.0/_/_/source/source_subfolder/libs/filesystem/src/operations.cpp:2799:24  
    #2 0x49fe1c in boost::filesystem::temp_directory_path() (/fsprob/build/fsprob+0x49fe1c)  
    #3 0x49a9f8 in main (/fsprob/build/fsprob+0x49a9f8)  
    #4 0x7f46792830b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)  
    #5 0x41f8bd in _start (/fsprob/build/fsprob+0x41f8bd)  
  
  Uninitialized value was created by an allocation of 'path_stat' in the stack frame of function '_ZN5boost10filesystem6detail6statusERKNS0_4pathEPNS_6system10error_codeE'  
    #0 0x4d0d90 in boost::filesystem::detail::status(boost::filesystem::path const&, boost::system::error_code*) /root/.conan/data/boost/1.75.0/_/_/source/source_subfolder/libs/filesystem/src/operations.cpp:2572  
  
SUMMARY: MemorySanitizer: use-of-uninitialized-value /root/.conan/data/boost/1.75.0/_/_/source/source_subfolder/libs/filesystem/src/operations.cpp:2603:7 in boost::filesystem::detail::status(boost::filesystem::path const&, boost::system::error_code*)  
Exiting  
~~~

---

## Comment 1

> Username: tonyelewis  
> Created at: 2021-04-28 14:05:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/185#issuecomment-828483954  

Files to repro (with spurious `.txt` extensions in some cases for attaching to GitHub):  
  
- [fsprob.cpp.txt](https://github.com/boostorg/filesystem/files/6392332/fsprob.cpp.txt)  
- [Dockerfile.txt](https://github.com/boostorg/filesystem/files/6392328/Dockerfile.txt)  
- [conanfile.txt](https://github.com/boostorg/filesystem/files/6392317/conanfile.txt)  
- [CMakeLists.txt](https://github.com/boostorg/filesystem/files/6392318/CMakeLists.txt)  
  
The errors with the original colouring codes:  
  
- [errors.with-colouring.txt](https://github.com/boostorg/filesystem/files/6392321/errors.with-colouring.txt)

---

## Comment 2

> Username: tonyelewis  
> Created at: 2021-04-28 14:07:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/185#issuecomment-828485638  

Note: in this case I'm not using a standard library built with the memory sanitiser enabled, but:  
  
1. the error stacktrace isn't going through the standard library  
2. I've seen the same error in another configuration that _does_ use a standard library built with the memory sanitiser enabled

---

## Comment 3

> Username: tonyelewis  
> Created at: 2021-04-28 14:26:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/185#issuecomment-828500366  

Ah - I see that there's a new Conan recipe for Boost 1.76.0. I've retested with 1.76.0 and the problem persists. I'll update the title of the issue to reflect that.  
  
Here is an updated [conanfile.txt](https://github.com/boostorg/filesystem/files/6392522/conanfile.txt).

---

## Comment 4

> Username: Lastique  
> Created at: 2021-05-16 20:31:12 UTC  
> Url: https://github.com/boostorg/filesystem/issues/185#issuecomment-841873427  

This is a false positive. The backtrace points to the check for `path_stat.stx_mask`, which must be set by the `statx` syscall.

---

## Comment 5

> Username: tonyelewis  
> Created at: 2021-06-04 11:01:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/185#issuecomment-854618502  

Thank you for your response. If there are are OS calls that can't be instrumented, can we annotate that in the Boost Filesystem code, so that users don't get lots of false positives when they try to memory-sanitize their code that uses the library?  
  
I tried creating a macro that expands to the appropriate attribute when compiling with the memory sanitizer enabled:  
  
~~~cpp  
#ifdef __has_feature  
#  if __has_feature(memory_sanitizer)  
#    define BOOST_NO_SANITIZE_MEMORY __attribute__((no_sanitize("memory")))  
#  else  
#    define BOOST_NO_SANITIZE_MEMORY  
#  endif  
#else  
#  define BOOST_NO_SANITIZE_MEMORY  
#endif  
~~~  
  
&hellip;and for my example, I put the following before the definition of `file_status status(const path &, error_code*)`:  
  
~~~cpp  
#if defined(BOOST_FILESYSTEM_HAS_STATX) || defined(BOOST_FILESYSTEM_HAS_STATX_SYSCALL)  
  BOOST_NO_SANITIZE_MEMORY  
#endif  
~~~  
  
&hellip;and that allowed my motivating example to run without complaint.  
  
Given that there seem to be quite a few calls to `statx()`, maybe it'd make sense to have an inline function decorated with `BOOST_NO_SANITIZE_MEMORY` that just forwards to `statx()`?  
  
---  
  
For reference, I tested this by getting the `Dockerfile` to build the Boost via Conan, and then doing an extra copy of the locally modified fs source file and forced rebuild:  
~~~docker  
COPY operations.cpp /root/.conan/data/boost/1.76.0/_/_/source/source_subfolder/libs/filesystem/src  
RUN conan install --build -s build_type=Debug --install-folder /fsprob/build /fsprob  
~~~
