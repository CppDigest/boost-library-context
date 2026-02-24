# #66 - Crashes LLDB on Windows [Closed]

> Username: everdrone  
> Created at: 2023-04-11 19:40:27 UTC  
> Updated at: 2023-04-25 08:31:35 UTC  
> Closed at: 2023-04-25 08:31:35 UTC  
> Url: https://github.com/boostorg/signals2/issues/66  

When using `clang++` with `c++20` the debugger crashes with the following message  
```  
error: example.exe :: Class 'std::strong_ordering' has a member 'less' of type 'std::strong_ordering' which does not have a complete definition.  
error: example.exe :: Class 'std::partial_ordering' has a member 'less' of type 'std::partial_ordering' which does not have a complete definition.  
error: example.exe :: Class 'std::weak_ordering' has a member 'less' of type 'std::weak_ordering' which does not have a complete definition.  
Debug adapter exit code=3221225477 (0xc0000005), signal=null.  
```  
  
Repository with simple reproductible example: https://github.com/everdrone/clang_signals2  
  
- Clang version:  
```  
clang version 15.0.7  
Target: x86_64-pc-windows-msvc  
Thread model: posix  
```  
- Windows 11, 22621  
- Boost 1.81.0  
- CMake 3.26.3  
- C++20

---

## Comment 1

> Username: fmhess  
> Created at: 2023-04-21 21:09:25 UTC  
> Url: https://github.com/boostorg/signals2/issues/66#issuecomment-1518338178  

I don't use windows, so I can't reproduce your problem.  FWIW, I was able to build your example on Linux and debug it with gdb without seeing any problem.

---

## Comment 2

> Username: fmhess  
> Created at: 2023-04-21 21:14:07 UTC  
> Url: https://github.com/boostorg/signals2/issues/66#issuecomment-1518341297  

I was also able to debug with lldb without seeing any problem.

---

## Comment 3

> Username: everdrone  
> Created at: 2023-04-25 08:31:20 UTC  
> Url: https://github.com/boostorg/signals2/issues/66#issuecomment-1521390562  

I guess I'll wait for other people to try to reproduce as it only happens on windows.  
  
Closing for now
