# #881 - Building issue on Windows on ARM64 with VS2022 for ARM64 native (1.83) [Closed]

> Username: BadaPZ  
> Created at: 2024-04-05 11:34:43 UTC  
> Updated at: 2024-06-18 12:26:08 UTC  
> Closed at: 2024-06-18 12:26:08 UTC  
> Url: https://github.com/boostorg/boost/issues/881  

Hi,  
No success with this build trying:  
at ARM64 native tools command prompt on a Windows on Arm64 (Azure VM)  
>bootstrap vc143  
>b2 abi=aapcs address-model=64 architecture=arm binary-format=pe threading=multi toolset=msvc link=static  
  
...patience...  
don't know how to make <p/C:/CSDK/MAIN/Development/3rdParty/boost_1_83_0>C:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.40.33617\bin\Hostarm64\vcvarsall.bat  
...patience...  
  
**(the bat actually is here: C:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Auxiliary\Build\vcvarsarm64.bat)**  
  
...patience...  
...found 11707 targets...  
...updating 565 targets...  
...can't find 1 target...  
...can't make 2 targets...  
...skipped <pbin.v2\standalone\msvc\msvc-14.3>msvc-setup.read for lack of <p/C:/CSDK/MAIN/Development/3rdParty/boost_1_83_0>C:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.40.33617\bin\Hostarm64\vcvarsall.bat...  
  
and many similar error messages  
  
...skipped 567 targets...  
  
Is there any advice or instruction for the success build? thanks

---

## Comment 1

> Username: BadaPZ  
> Created at: 2024-04-18 08:24:25 UTC  
> Url: https://github.com/boostorg/boost/issues/881#issuecomment-2063309168  

Could anybody build boost on WoARM64 using VS2022?

---

## Comment 2

> Username: BadaPZ  
> Created at: 2024-06-10 06:26:17 UTC  
> Url: https://github.com/boostorg/boost/issues/881#issuecomment-2157421170  

It looks, it is vc144 toolset handling issue in b2

---

## Comment 3

> Username: BadaPZ  
> Created at: 2024-06-18 12:26:08 UTC  
> Url: https://github.com/boostorg/boost/issues/881#issuecomment-2175977700  

This https://github.com/boostorg/boost/issues/914 solves this issue.  
boots builds and runs on Windows on ARM64
