# #621 - Cannot build in VS2017 wrong path to vcvarsall.bat [Open]

> Username: smiddy  
> Created at: 2019-03-13 10:22:09 UTC  
> Updated at: 2021-05-29 17:22:34 UTC  
> Url: https://github.com/boostorg/build/issues/621  

Boost Version: 1.69  
System: Win 10, 64 bit  
Using: Visual Studio Build Tools 2017, v15.9.28307.423  
  
I tried to compile the Boost library with VS2017, utilizing `x64 native Tools Command Prompt for VS2017`:  
  
No problems with bootstrap:  
```C:\Users\schmmark\Downloads\boost_1_69_0\boost_1_69_0>bootstrap.bat  
Building Boost.Build engine  
  
Bootstrapping is done. To build, run:  
  
    .\b2  
  
To adjust configuration, edit 'project-config.jam'.  
Further information:  
  
    - Command line help:  
    .\b2 --help  
  
    - Getting started guide:  
    http://boost.org/more/getting_started/windows.html  
  
    - Boost.Build documentation:  
    http://www.boost.org/build/doc/html/index.html  
```  
  
The following compilations succeed:  
- `b2 address-model=64` results in `vc141` code as expected  
- `b2 toolset=msvc-14.0 address-model=64` succeeds  
- `b2 toolset=msvc-12.0 address-model=64` fails  
  
The error is  
`don't know how to make <p/C:/Users/schmmark/Downloads/boost_1_69_0/boost_1_69_0>C:\Program Files (x86)\Microsoft Visual Studio\2017\BuildTools\VC\Tools\MSVC\14.16.27023\bin\Hostx64\vcvarsall.bat`  
  
There is a discussion here:  
https://groups.google.com/forum/#!topic/boost-list/sOy30ec_isU  
  
Are changes already implemented?

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:04 UTC  
> Url: https://github.com/boostorg/build/issues/621#issuecomment-850868268  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
