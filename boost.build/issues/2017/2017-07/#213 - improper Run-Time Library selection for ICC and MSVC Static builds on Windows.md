# #213 - improper Run-Time Library selection for ICC and MSVC Static builds on Windows [Open]

> Username: sav-ix  
> Created at: 2017-07-16 11:59:03 UTC  
> Updated at: 2017-07-16 11:59:03 UTC  
> Url: https://github.com/boostorg/build/issues/213  

Hello, everyone,  
  
For BOOST builds using ICC and MSVC with Static layout got warnings:  
```  
cl : Command line warning D9025 : overriding '/MDd' with '/MTd'  
```  
for Debug configuration, and  
```  
cl : Command line warning D9025 : overriding '/MD' with '/MT'  
```  
for Release configuration.  
  
Reproduced, if `/MTd` and `/MT` keys are explicitly set using `cxxflags` variable.  
  
The possible workaround is to remove `/MDd` and `/MD` keys from [msvc.jam](https://github.com/boostorg/build/blob/develop/src/tools/msvc.jam) before build and select Run-Time Library using `cxxflags` variable. But it would be much convenient, if appropriate Run-Time Libraries were selected according its [description](https://msdn.microsoft.com/en-us/library/2kzt1wy3.aspx) by default. If there is no reason to do otherwise, of course.  
  
Environment:  
- Windows 10 x64,  
- Intel Parallel Studio XE 2017 Update 4,  
- Visual Studio 2015 Update 3,  
- BOOST-1.64.0.  
  
  
Best,  
  
Alexander
