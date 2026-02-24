# #35 - Documentation Issue using MinGW [Closed]

> Username: githubuser0xFFFF  
> Created at: 2017-11-03 07:43:36 UTC  
> Updated at: 2017-11-05 17:13:13 UTC  
> Closed at: 2017-11-05 17:13:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/35  

We use the MinGW toolchain that ships with the Qt Libraries. This is the output of gcc -v  
  
```  
Thread model: posix  
gcc version 5.3.0 (i686-posix-dwarf-rev0, Built by MinGW-W64 project)  
```  
  
Using **BOOST_STACKTRACE_USE_WINDBG** with MinGW results in linker errors because the following libraries are required: **ole32** and **dbgeng**. This is not documented anywhere.
