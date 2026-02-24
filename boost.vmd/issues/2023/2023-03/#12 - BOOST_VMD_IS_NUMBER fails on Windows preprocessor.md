# #12 - BOOST_VMD_IS_NUMBER fails on Windows preprocessor [Closed]

> Username: vinipsmaker  
> Created at: 2023-03-04 01:48:02 UTC  
> Updated at: 2023-03-04 21:57:43 UTC  
> Closed at: 2023-03-04 21:57:43 UTC  
> Url: https://github.com/boostorg/vmd/issues/12  

So, this only happens on Windows. The same code on Linux will work. `clang-cl` and `clang` will also fail on Windows with the same error (however `clang` does the correct thing on Linux). It's a pretty bizarre bug, I'm not really sure what's going on. If I get the code block where the error occurs and put it on a small file to isolate the issue, the error vanishes and the compiler does its job correctly (so unfortunately I need to paste the whole failing file here). I also tried to use `clang -E` to see what's going on, but it won't generate anything on this failure.  
  
Here's the error output:  
  
```  
"clang" "-Iemilua.exe.p" "-Iinclude" "-I..\include" "-I..\subprojects\fmt-8.1.1\include" "-I..\subprojects\serd-0.30.16\include" "-I..\subprojects\sord-0.16.14\include" "-I..\subprojects\trial-protocol\include" "-I..\subprojects\emilua-http\include" "-IC:/local/boost_1_81_0" "-IC:/vcpkg/packages/luajit_x64-windows-static-release/include/luajit" "-IC:/Program Files/OpenSSL-Win64/include" "-fcolor-diagnostics" "-D_FILE_OFFSET_BITS=64" "-Wall" "-Winvalid-pch" "-std=c++20" "-O3" "-DBOOST_DLL_USE_STD_FS" "-DBOOST_ASIO_DISABLE_CO_AWAIT" "-DBOOST_ASIO_DISABLE_STD_COROUTINE" "-DBOOST_BEAST_USE_STD_STRING_VIEW" "-DFMT_HEADER_ONLY" "-D_WIN32_WINNT=0x0602" "-DSORD_STATIC" "-DSERD_STATIC" "-DBOOST_NOWIDE_DYN_LINK=1" "-DBOOST_ALL_NO_LIB" "-pthread" -o emilua.exe.p/src_system.cpp "-E" ../src/system.cpp  
../src/system.cpp:601:13: error: pasting formed ')_BOOST_VMD_TYPE_NUMBER', an invalid preprocessing token  
      [-Winvalid-token-paste]  
            EMILUA_IS_SIG_X_OR(SIGKILL)  
            ^  
../src/system.cpp:585:9: note: expanded from macro 'EMILUA_IS_SIG_X_OR'  
        BOOST_VMD_IS_NUMBER(SIG), EMILUA_DETAIL_IS_SIG_X_OR, BOOST_VMD_EMPTY \  
        ^  
C:/local/boost_1_81_0\boost/vmd/is_number.hpp:48:5: note: expanded from macro 'BOOST_VMD_IS_NUMBER'  
    BOOST_VMD_DETAIL_IS_NUMBER(sequence) \  
    ^  
C:/local/boost_1_81_0\boost/vmd/detail/is_number.hpp:31:13: note: expanded from macro 'BOOST_VMD_DETAIL_IS_NUMBER'  
            BOOST_VMD_DETAIL_IS_NUMBER_TYPE, \  
            ^  
```  
  
Here's the failing code block (and the whole file):  
  
https://github.com/vinipsmaker/emilua/blob/346ebb64ba58e4a799821dcd76e86b7b8bac94ce/src/system.cpp#L579-L607  
  
The idea here is to use `SIG*` macros indiscriminately such that they will be available when the host system provides them, so I don't need to worry whether I'm running on Windows, Linux, or any of the many BSD variants. `BOOST_VMD_IS_NUMBER()` really helps me here. However, this pattern (I use it a lot on the same file... not just for obvious macros such as `SIGKILL` and `SIGSTOP`) is failing on Windows.  
  
Does the implementation relies on code line numbers or something (given I was unable to reproduce the issue when I've tried to isolate it on a small separate file)?  
  
Are you aware of a generally available C preprocessor that I could use on Windows and supports Boost.VMD? [It'll be a pain to set it in Meson given there's no explicit variable there](https://mesonbuild.com/Reference-tables.html#compiler-and-linker-selection-variables), but if that's what it takes to have Boost.VMD on Windows, I'm willing to take the pain.

---

## Comment 1

> Username: vinipsmaker  
> Created at: 2023-03-04 21:13:42 UTC  
> Url: https://github.com/boostorg/vmd/issues/12#issuecomment-1454883217  

I managed to isolate the issue in a minimal source file. Here's a file showcasing the same build failure, but only including Boost.PP and Boost.VMD headers (and `<csignal>`):  
  
https://gist.github.com/vinipsmaker/b4d857402a98bf9582e58e1423cc4325

---

## Comment 2

> Username: vinipsmaker  
> Created at: 2023-03-04 21:57:43 UTC  
> Url: https://github.com/boostorg/vmd/issues/12#issuecomment-1454899988  

I've been unable to reproduce the issue on MSVC's `cl.exe`, so I believe this is a clang issue and I'm closing this issue. I reported the issue to LLVM in case anyone is interested in following: https://github.com/llvm/llvm-project/issues/61184
