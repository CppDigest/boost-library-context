# #91 - Problem building 1.81.0 on MSVC 14.3 (VS2022 17.6.2) with sanitizer [Closed]

> Username: andshiga  
> Created at: 2023-06-12 15:23:21 UTC  
> Updated at: 2023-06-13 11:15:29 UTC  
> Closed at: 2023-06-13 08:47:37 UTC  
> Url: https://github.com/boostorg/winapi/issues/91  

I haven't been able to build boost using Visual Studio 2022-14.3 on Debug x64 configuration with sanitizer enabled. I am seeing a few issues. The first is a library naming problem when linking with errors such as:  
`msvc.link.dll bin.v2\libs\url\build\msvc-14.3\debug\address-sanitizer-on\threading-multi\boost_url-vc143-mt-gd-x64-1_81-asan.dll  
clang_rt.asan_dbg_dynamic_runtime_thunk-x86_64.lib(sanitizer_win_dynamic_runtime_thunk.cpp.obj) : error LNK2005: "void __cdecl __sanitizer::ForceWholeArchiveIncludeForSanitizerCommon(void)" (?ForceWholeArchiveIncludeForSanitizerCommon@__sanitizer@@YAXXZ) already defined in clang_rt.asan_dynamic_runtime_thunk-x86_64.lib(sanitizer_win_dynamic_runtime_thunk.cpp.obj)  
clang_rt.asan_dbg_dynamic_runtime_thunk-x86_64.lib(sanitizer_coverage_win_sections.cpp.obj) : error LNK2005: __start___sancov_cntrs already defined in clang_rt.asan_dynamic_runtime_thunk-x86_64.lib(sanitizer_coverage_win_sections.cpp.obj)  
clang_rt.asan_dbg_dynamic_runtime_thunk-x86_64.lib(sanitizer_coverage_win_sections.cpp.obj) : error LNK2005: __stop___sancov_cntrs already defined in clang_rt.asan_dynamic_runtime_thunk-x86_64.lib(sanitizer_coverage_win_sections.cpp.obj)`  
I managed to get around this problem by changing the references to the library(ies) above from non-dbg to dbg library names in tools\build\src\tools\msvc.jam lines 504-556. E.g.:   
Line 504: clang_rt.asan_dynamic-x86_64 -> clang_rt.asan_dbg_dynamic-x86_64  
Line 505: clang_rt.asan_dynamic_runtime_thunk-x86_64 -> clang_rt.asan_dbg_dynamic_runtime_thunk-x86_64  
and so forth...  
  
Past this issue, I've found another issue where it can't find a couple of Windows API functions:  
`compile-c-c++ bin.v2\libs\nowide\build\msvc-14.3\debug\address-sanitizer-on\threading-multi\cstdio.obj  
cstdio.cpp  
msvc.link.dll bin.v2\libs\log\build\msvc-14.3\debug\address-sanitizer-on\threadapi-win32\threading-multi\boost_log-vc143-mt-gd-x64-1_81-asan.dll  
   Creating library bin.v2\libs\log\build\msvc-14.3\debug\address-sanitizer-on\threadapi-win32\threading-multi\boost_log-vc143-mt-gd-x64-1_81-asan.lib and object bin.v2\libs\log\build\msvc-14.3\debug\address-sanitizer-on\threadapi-win32\threading-multi\boost_log-vc143-mt-gd-x64-1_81-asan.exp  
event.obj : error LNK2019: unresolved external symbol WaitOnAddress referenced in function "public: static unsigned int __cdecl boost::atomics::detail::wait_operations_windows<struct boost::atomics::detail::core_operations<4,0,0>,4>::wait(unsigned int const volatile &,unsigned int,enum boost::memory_order)" (?wait@?$wait_operations_windows@U?$core_operations@$03$0A@$0A@@detail@atomics@boost@@$03@detail@atomics@boost@@SAIAEDIIW4memory_order@4@@Z)  
event.obj : error LNK2019: unresolved external symbol WakeByAddressSingle referenced in function "public: static void __cdecl boost::atomics::detail::wait_operations_windows<struct boost::atomics::detail::core_operations<4,0,0>,4>::notify_one(unsigned int volatile &)" (?notify_one@?$wait_operations_windows@U?$core_operations@$03$0A@$0A@@detail@atomics@boost@@$03@detail@atomics@boost@@SAXAECI@Z)  
bin.v2\libs\log\build\msvc-14.3\debug\address-sanitizer-on\threadapi-win32\threading-multi\boost_log-vc143-mt-gd-x64-1_81-asan.dll : fatal error LNK1120: 2 unresolved externals`  
I have also found a way to get around this problem by commenting out the `#if defined(BOOST_USE_WINDOWS_H)` in boost\winapi\wait_on_address.hpp line 22.  
Is this something I am missing or support that should be added to sanitizer on msvc 14.3?

---

## Comment 1

> Username: Lastique  
> Created at: 2023-06-13 08:47:36 UTC  
> Url: https://github.com/boostorg/winapi/issues/91#issuecomment-1588838346  

I cannot comment on the sanitizer issues, other than if the library names are incorrect in `msvc.jam` then this is a Boost.Build issue and should be reported [there](https://github.com/bfgroup/b2/).  
  
The other issue about unresolved symbols seem to be a duplicate of [this](https://github.com/boostorg/log/issues/177). You should check if `synchronization.lib` is in the command line when you build Boost.Log. If not, please report it to Boost.Build as well (as a separate issue) because the dependency is added by Boost.Atomic [here](https://github.com/boostorg/atomic/blob/8915f67c0e7ab8e2551570e7c5b1a9b98b902153/build/Jamfile.v2#L143). You can see the compiler and linker command lines if you add `-d+2` to `b2` command line.  
  
Neither of the problems is related to Boost.WinAPI, so I'm going to close this.

---

## Comment 2

> Username: Lastique  
> Created at: 2023-06-13 10:26:08 UTC  
> Url: https://github.com/boostorg/winapi/issues/91#issuecomment-1589008963  

Also, re. `synchronization.lib`, check if "has synchronization.lib : yes" is displayed at the beginning of your build. If it says "no", then there's something wrong with your Windows SDK installation, or perhaps your build environment. Boost.Build should be able to detect Visual Studio installation paths automatically, so that the necessary library paths are added to the linker lookup paths, but just in case it doesn't, try running the build from the Visual Studio console.

---

## Comment 3

> Username: andshiga  
> Created at: 2023-06-13 10:55:06 UTC  
> Url: https://github.com/boostorg/winapi/issues/91#issuecomment-1589063511  

Yes, I tried it on a VS command prompt and it worked. I need to find out what's missing from my regular cmd (had the sync lib missing). Thank you very much for pointing that out!

---

## Comment 4

> Username: Lastique  
> Created at: 2023-06-13 11:07:45 UTC  
> Url: https://github.com/boostorg/winapi/issues/91#issuecomment-1589081405  

Do you have Visual Studio installed in a non-default path?  
  
Unfortunately, I don't know exactly how the VS path detection works in Boost.Build these days. I think, it used `vswhere.exe` at some point, and probed registry keys at another, but I haven't been following the development. In any case, if there is a way to improve detection of the header/library paths, you should report it in [Boost.Build](https://github.com/bfgroup/b2/).

---

## Comment 5

> Username: andshiga  
> Created at: 2023-06-13 11:15:28 UTC  
> Url: https://github.com/boostorg/winapi/issues/91#issuecomment-1589092265  

I need to run vcvars64.bat (or vcvars32.bat or vcvarsall.bat depending on what I wanna build) on a regular command prompt to have the environment properly set. This batch file is not present on the system path by default so I already call it beforehand on other projects.
