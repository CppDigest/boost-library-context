# #107 - sprintf deprecated [Open]

> Username: sdarwin  
> Created at: 2025-08-21 14:44:22 UTC  
> Updated at: 2025-08-21 14:44:22 UTC  
> Url: https://github.com/boostorg/format/issues/107  

sprintf is deprecated on macos clang.  
  
"Use snprintf if available as some compilers (clang 14.0) issue deprecation warnings for sprintf"  
  
sprintf appears in these files:    
  
```  
libs/format/benchmark/bench_format.cpp:// bench_variants.cc :  do the same task, with sprintf, stream, and format  
libs/format/benchmark/bench_format.cpp:    // Check that sprintf is Unix98 compatible on the platform :  
libs/format/benchmark/bench_format.cpp://      (e.g., it expects sprintf to be POSIX compliant)  
libs/format/benchmark/bench_format.cpp:#include <cstdio>  // sprintf  
libs/format/benchmark/bench_format.cpp:      sprintf(buf, fstring.c_str(), arg1, arg2, arg3);  
libs/format/benchmark/bench_format.cpp:    sprintf(buf, fstring.c_str(), arg1, arg2, arg3);  
```
