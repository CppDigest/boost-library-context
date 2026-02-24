# #321 - CPU architecture detection isn't correct for MSVC targeting ARM64. [Closed]

> Username: hksdpc255  
> Created at: 2021-04-16 10:00:56 UTC  
> Updated at: 2021-06-04 12:46:41 UTC  
> Closed at: 2021-06-04 12:46:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/321  

See: https://github.com/microsoft/vcpkg/issues/17312  
  
It seems like the boost library is using x86-only feature when building a ARM64 project.
