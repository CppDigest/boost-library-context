# #983 - Windows on ARM64 (aarch64-w64-mingw32) winfib build using MinGW-w64 fails (undefined jump_fcontext) [Open]

> Username: brechtsanders  
> Created at: 2024-11-17 10:19:59 UTC  
> Updated at: 2025-08-14 11:18:47 UTC  
> Url: https://github.com/boostorg/boost/issues/983  

When trying to build a aarch64-w64-mingw32 build (native on Windows on ARM using this [LLVM/CLang](https://github.com/mstorsjo/llvm-mingw)) with `-DBOOST_CONTEXT_IMPLEMENTATION:STRING=winfib` there the following error occcurs:  
```raw  
undefined symbol: __declspec(dllimport) jump_fcontext  
```  
This only happens with a shared build. A static build works fine (`-DBUILD_SHARED_LIBS:BOOL=OFF -DBOOST_RUNTIME_LINK:STRING=static`).  
  
Full error output:  
```raw  
ld.lld: error: undefined symbol: __declspec(dllimport) make_fcontext  
>>> referenced by libs/coroutine/CMakeFiles/boost_coroutine.dir/src/detail/coroutine_context.cpp.obj:(boost::coroutines::detail::coroutine_context::coroutine_context(void (*)(boost::context::detail::transfer_t), boost::coroutines::detail::preallocated const&))  
>>> referenced by libs/coroutine/CMakeFiles/boost_coroutine.dir/src/detail/coroutine_context.cpp.obj:(boost::coroutines::detail::coroutine_context::coroutine_context(void (*)(boost::context::detail::transfer_t), boost::coroutines::detail::preallocated const&))  
  
ld.lld: error: undefined symbol: __declspec(dllimport) jump_fcontext  
>>> referenced by libs/coroutine/CMakeFiles/boost_coroutine.dir/src/detail/coroutine_context.cpp.obj:(boost::coroutines::detail::coroutine_context::jump(boost::coroutines::detail::coroutine_context&, void*))  
>>> referenced by libs/coroutine/CMakeFiles/boost_coroutine.dir/src/detail/coroutine_context.cpp.obj:(boost::coroutines::detail::coroutine_context::jump(boost::coroutines::detail::coroutine_context&, void*))  
clang-19: error: linker command failed with exit code 1 (use -v to see invocation)  
[79/435] Building CXX object libs/serialization/CMakeFiles/boost_serialization.dir/src/xml_grammar.cpp.obj  
ninja: build stopped: subcommand failed.  
```  
  
As mentioned in #982 builds with `-DBOOST_CONTEXT_IMPLEMENTATION:STRING=fcontext` also fail, so I don't seem to have a proper MinGW-w64 build of Boost for Windows on ARM64 (`aarch64-w64-mingw32`).

---

## Comment 1

> Username: Greenie0701  
> Created at: 2025-08-12 05:24:38 UTC  
> Url: https://github.com/boostorg/boost/issues/983#issuecomment-3177751891  

@brechtsanders can you try with MSVC by pulling out latest dev branch and try to reproduce this?

---

## Comment 2

> Username: brechtsanders  
> Created at: 2025-08-12 06:01:04 UTC  
> Url: https://github.com/boostorg/boost/issues/983#issuecomment-3177823731  

The goal of winlibs.com is not to use MSVC, only MinGW-w64.  
So I don't even have MSVC on my systems.

---

## Comment 3

> Username: Greenie0701  
> Created at: 2025-08-12 06:17:50 UTC  
> Url: https://github.com/boostorg/boost/issues/983#issuecomment-3177860228  

I was hitting a similar issue with Boost even with MSVC too, the only fix I was able to find is to exclude coroutine targets of boost and the build was successful with cmake.  
  
-DBOOST_EXCLUDE_LIBRARIES='mpi;graph_parallel;coroutine'

---

## Comment 4

> Username: Greenie0701  
> Created at: 2025-08-12 06:29:06 UTC  
> Url: https://github.com/boostorg/boost/issues/983#issuecomment-3177885066  

I think the issue is due to poor identification/compilation of corresponding assembly implementation of context sources which is causing this issue. Probably due to some CMake bugs or compiler config bug causing this issue.

---

## Comment 5

> Username: Greenie0701  
> Created at: 2025-08-14 11:18:47 UTC  
> Url: https://github.com/boostorg/boost/issues/983#issuecomment-3188087905  

> I was hitting a similar issue with Boost even with MSVC too, the only fix I was able to find is to exclude coroutine targets of boost and the build was successful with cmake.  
>   
> -DBOOST_EXCLUDE_LIBRARIES='mpi;graph_parallel;coroutine'  
  
@brechtsanders did you try this?
