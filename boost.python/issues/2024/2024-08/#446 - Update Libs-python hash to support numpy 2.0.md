# #446 - Update Libs/python hash to support numpy 2.0 [Closed]

> Username: NEIL-smtg  
> Created at: 2024-08-02 03:08:48 UTC  
> Updated at: 2024-10-04 05:18:38 UTC  
> Closed at: 2024-10-04 05:18:38 UTC  
> Url: https://github.com/boostorg/python/issues/446  

I work on MSVC compiler testing, and we regularly build popular open-source project including Boost, with development builds of MSVC in order to find and fix regressions before they ship and cause problems for you.   
  
Recently Boost encountered error when building with MSVC under the c++latest mode:  
```c++  
C:\boost\libs\python\src\numpy\dtype.cpp(101): error C2039: 'elsize': is not a member of '_PyArray_Descr'  
C:\Python312\Lib\site-packages\numpy\_core\include\numpy\ndarraytypes.h(618): note: see declaration of '_PyArray_Descr'  
```  
  
I believe that this issue is because [python @ 47d5bc7](https://github.com/boostorg/python/tree/47d5bc76f69e20625214381c930a2fad5765e2b3) does not have support for numpy version >= 2.0, but recently they have committed a [fix ](https://github.com/boostorg/python/commit/0474de0f6cc9c6e7230aeb7164af2f7e4ccf74bf) for this issue. Any chance you guys can update the hash of libs/python?  
  
Steps to reproduce:  
  
1. Open command prompt  
2. git clone https://github.com/boostorg/boost.git  
3. cd boost  
4. git submodule sync  
5. git submodule foreach git reset --hard  
6. set VSCMD_SKIP_SENDTELEMETRY=1 & "C:\Program Files\Microsoft Visual Studio\2022\Enterprise\Common7\Tools\VsDevCmd.bat" -host_arch=amd64 -arch=amd64 & set _CL_ = /std:c++latest /D_SILENCE_EXPERIMENTAL_FILESYSTEM_DEPRECATION_WARNING /DBOOST_TIMER_ENABLE_DEPRECATED /wd4996 /bigobj /D_HAS_DEPRECATED_ADAPTOR_TYPEDEFS=1 /D_HAS_AUTO_PTR_ETC=1 /D_HAS_DEPRECATED_RAW_STORAGE_ITERATOR=1 /D_HAS_DEPRECATED_TEMPORARY_BUFFER=1 /D_HAS_DEPRECATED_NEGATORS=1 /Zc:enumTypes /Zc:equalityRewrite-  
7. .\bootstrap 2>&1  
8. .\b2 headers variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86 2>&1  
9. .\b2 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86 2>&1  
  
Log: [Build.log](https://github.com/user-attachments/files/16464786/Build.log)  
OS: Windows

---

## Comment 1

> Username: BwL1289  
> Created at: 2024-08-12 13:13:10 UTC  
> Url: https://github.com/boostorg/python/issues/446#issuecomment-2283951266  

Also experiencing on Linux

---

## Comment 2

> Username: h-vetinari  
> Created at: 2024-08-15 06:04:57 UTC  
> Url: https://github.com/boostorg/python/issues/446#issuecomment-2290730689  

It's pointless to move this from boostorg/boost to boostorg/python. This issue was fixed in the latter, but the submodule in the main repo wasn't updated yet.

---

## Comment 3

> Username: NEIL-smtg  
> Created at: 2024-10-04 05:18:38 UTC  
> Url: https://github.com/boostorg/python/issues/446#issuecomment-2392842439  

The issue has been resolved since main repo has updated the commit of python, closing this issue.
