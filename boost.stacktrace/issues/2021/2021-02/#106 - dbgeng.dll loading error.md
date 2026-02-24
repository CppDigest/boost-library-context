# #106 - dbgeng.dll loading error [Closed]

> Username: LukeMauldin  
> Created at: 2021-02-26 20:35:16 UTC  
> Updated at: 2024-12-16 11:36:29 UTC  
> Closed at: 2024-09-12 07:51:46 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/106  

Compiler: MSVC 2019 - 16.8.5 x64  
Operating system: Windows Server 2019  
Build Mode: Release with Debug Info  
Boost version: 1.74.0  
  
When trying to use boost stacktrace functionality on Windows in an executable, the executable builds, links, and runs as expected and the application stack trace is displayed as expected.  When trying to use boost stacktrace functionality in a .DLL, the follow error messaging is presented by the application on DLL load time:  `The original 1002 could not be located in the dynamic link library C:\Windows\SYSTEM32\dbgeng.dll`.  I have verified that the DLL exists and like I mentioned previously, the boost stacktrace functionality does work in an executable.  
I have tried building boost stacktrace as both a header only library and linking in as a .dll using the define: `BOOST_STACKTRACE_LINK`.

---

## Comment 1

> Username: oold  
> Created at: 2023-03-21 14:01:31 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/106#issuecomment-1477891812  

> The original 1002  
  
Shouldn't that be the "ordinal 1002"?  
  
Basically, the entry point is missing in `dbgeng.dll`. I've seen this before when trying to run an executable that consumes Boost.Stacktrace on another machine. Seems that it's not safe to link against Stacktrace and run the resulting binary on any machine other than the machine it was built on (for Windows targets).

---

## Comment 2

> Username: apolukhin  
> Created at: 2024-09-12 07:51:46 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/106#issuecomment-2345519561  

Try to make a minimized reproducer of the problem and report it to the Windows/MS developers team

---

## Comment 3

> Username: oold  
> Created at: 2024-12-16 11:36:28 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/106#issuecomment-2545383360  

I would recommend looking at https://github.com/microsoft/STL/blob/7643c270e5bfb1cfad62f8b5ff4045c662bdaf81/stl/src/stacktrace.cpp#L75. This is how the Microsoft C++ standard library initializes the Debug Engine and it definitely works without the issues observed with this implementation. `dbgeng.dll` is loaded at runtime via `LoadLibraryExW()` and the `DebugCreate()` function pointer is retrieved via `GetProcAddress()`.
