# #574 - clang-linux target triple [Closed]

> Username: eldiener  
> Created at: 2020-04-21 09:13:00 UTC  
> Updated at: 2020-04-22 23:52:44 UTC  
> Closed at: 2020-04-22 23:52:44 UTC  
> Url: https://github.com/boostorg/build/issues/574  

The default target triple, as represented by the "-target" command line parameter, for clang depends on the installation and the address model. On Windows installations of clang in LLVM releases the default targer triple changed form targeting gcc to targeting vc++ with clang-3.8. The target triple chosen affects the validity of which command line parameters are recognized as valid and how those command line paramneters are interpreted.  
  
In the clang-linux.jam toolset the command line parameters conform to a target triple representing gcc as the target, ie. 'i686-pc-windows-gnu' for a 32-bit build and 'x86_64-pc-windows-gnu' for a 64-bit build. If clang-linux is used with the default target triple in LLVM release 3.8.0 on up for Windows, without the target triple specifically being set to target gcc, the clang-linux toolset will fail miserably when run on Windows using such an LLVM release. We have a separate toolset, clang-win, for using the default target triple for LLVM Windows releases 3.8 on up which is to target vc++.  
  
In the Boost Build tests, the 'toolset_clang_linux' works fine when run in Linux because the default target triple for clang-3.9.0 on Linux targets gcc. But when run on Windows it fails because the default target triple targets vc++.   
  
How should this be resolved ?  
  
In my own clang-linux toolset definitions I have the correct target triple for 32-bit and 64-bit builds set in the toolset definition as <compileflags> and <linkflags> so using the clang-linux toolset works properly.

---

## Comment 1

> Username: eldiener  
> Created at: 2020-04-22 23:52:44 UTC  
> Url: https://github.com/boostorg/build/issues/574#issuecomment-618097642  

I can now see that the Boost Buiuld test system never invokes 'clang++' in the 'toolset_clang_linux' test, so I will close this issue.
