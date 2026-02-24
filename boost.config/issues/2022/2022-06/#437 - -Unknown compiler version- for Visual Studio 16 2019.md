# #437 - "Unknown compiler version" for Visual Studio 16 2019 [Closed]

> Username: Flamefire  
> Created at: 2022-06-10 10:33:58 UTC  
> Updated at: 2022-06-13 15:35:25 UTC  
> Closed at: 2022-06-13 15:30:52 UTC  
> Url: https://github.com/boostorg/config/issues/437  

When running the CMake build on the windows-2019 image with the "Visual Studio 16 2019" generator I get multiple `  Unknown compiler version - please run the configure tests and report the results`  
  
See [this build](https://github.com/boostorg/nowide/runs/6818485566?check_suite_focus=true)  
  
Possibly useful info:  
  
>- The CXX compiler identification is MSVC 19.29.30145.0  
>- Check for working CXX compiler: C:/Program Files (x86)/Microsoft Visual Studio/2019/Enterprise/VC/Tools/MSVC/14.29.30133/bin/Hostx64/x64/cl.exe - skipped  
> - Microsoft (R) Build Engine version 16.11.2+f32259642 for .NET Framework  
  
Using the develop branch. I wasn't able to find the file from which this warnings is generated and don't have access to that compiler outside of CI either.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-06-13 15:30:52 UTC  
> Url: https://github.com/boostorg/config/issues/437#issuecomment-1154070338  

That suggests that you are testing against an old (very old actually) version of Boost, as that error hasn't been around for quite a few releases.  Plus we definitely do support msvc-2019 !

---

## Comment 2

> Username: Flamefire  
> Created at: 2022-06-13 15:35:25 UTC  
> Url: https://github.com/boostorg/config/issues/437#issuecomment-1154075175  

Woops, you are right. Got confused...  
  
> D:/a/nowide/nowide/dependencies/boost_1_56_0
