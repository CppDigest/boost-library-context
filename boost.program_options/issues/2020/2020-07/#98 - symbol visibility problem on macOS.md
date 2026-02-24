# #98 - symbol visibility problem on macOS [Open]

> Username: dvhwgumby  
> Created at: 2020-07-20 00:31:28 UTC  
> Updated at: 2022-02-15 05:37:48 UTC  
> Url: https://github.com/boostorg/program_options/issues/98  

When I compile with gcc-9, gcc-10, clang-9, clang-10 or the Xcode clang I have this problem.  I do not see it under Linux (Ubuntu 18 LTS):  
[BUG-REPORT.txt](https://github.com/boostorg/program_options/files/4944925/BUG-REPORT.txt)  
  
  
  
I get linkage warnings that suggest possible runtime failure  
  
    ld: warning: direct access in function '__ZN5boost10wrapexceptINS_15program_options14unknown_optionEEC1ERKS3_' from file lib/libboost_program_options.a(options_description.o)' to global weak symbol '__ZTVN5boost15program_options5errorE' from file XXX means the weak symbol cannot be overridden at runtime. This was likely caused by different translation units being compiled with different visibility settings.  
  
What's weird is that visibility should only apply to dynamic linkage, and I have built the libraries entirely statically.  
  
I was able to get around this by building all of boost by supplying `cxxflags="-fvisibility=default"` to b2.  Probably not the right fix but make the problem go away.  
  
Probably incomplete list of symbols causing this problem.  I do not have this issue with any other Boost library I use.

---

## Comment 1

> Username: sarrow104  
> Created at: 2022-02-15 05:37:47 UTC  
> Url: https://github.com/boostorg/program_options/issues/98#issuecomment-1039879031  

same problem with vcpkg and boost 1.78
