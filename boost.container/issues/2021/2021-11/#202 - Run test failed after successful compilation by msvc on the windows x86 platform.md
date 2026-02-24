# #202 - Run test failed after successful compilation by msvc on the windows x86 platform [Closed]

> Username: qd1332543  
> Created at: 2021-11-24 08:53:49 UTC  
> Updated at: 2022-01-07 00:35:29 UTC  
> Closed at: 2022-01-07 00:35:29 UTC  
> Url: https://github.com/boostorg/container/issues/202  

**Describe the bug**  
When I used the code compiled by msbuild for unit testing, two test failed. The specific failure is as follows：  
  
```  
libs\container\test:  
...failed compile-c-c++ ..\out\x86rel\boost\bin.v2\libs\container\test\string_test.test\msvc-14.2\release\threading-multi\string_test.obj...  
```  
  
**To Reproduce**  
Steps to reproduce the behavior:  
  
1. git clone https://github.com/boostorg/boost f:boostorg  
1. git -C "F:\boostorg\boost" rev-parse --git-dir  
git -C "F:\boostorg\boost" clean -xdf  
git -C "F:\boostorg\boost" fetch --recurse-submodules=no --force  
git -C "F:\boostorg\boost" reset --hard 339c893  
git -C "F:\boostorg\boost" remote prune origin  
git -C "F:\boostorg\boost" submodule sync --recursive  
git -C "F:\boostorg\boost" submodule foreach git reset --hard  
git -C "F:\boostorg\boost" submodule foreach git clean -xdf  
git -C "F:\boostorg\boost" submodule update -f --init --recursive  
1. set VSCMD_SKIP_SENDTELEMETRY=1 & "C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\Common7\Tools\VsDevCmd.bat" -host_arch=x86 -arch=x86  
cd F:\boostorg\boost  
set NO_ZLIB=0  
set NO_BZIP2=0  
set path=F:\gitP\boostorg\OpenSSL\bin;%path%  
set INCLUDE=F:\gitP\boostorg\OpenSSL\include;%INCLUDE%  
set Lib=F:\gitP\boostorg\OpenSSL\lib;%Lib%  
set _CL_=/wd4643 %_CL_%  
.\bootstrap  
.\b2 headers variant=release --build-dir=..\out\x86rel address-model=32  
.\b2 variant=release --build-dir=..\out\x86rel address-model=32  
1. .\b2 -j16 variant=release --build-dir=..\out\x86rel libs\container\test   
  
**Expected behavior**  
All test pass.  
  
**Observed Behavior**  
Test failed.  
  
**Environment**  
OS: Windows Server 2019  
CPU: AMD EPYC 7452 32-Core Processor  
Compiler: MSVC 14.29.30133  
Language: ENU  
  
**Note**  
You can also check build error messages from attached file   
[test_02.log](https://github.com/boostorg/boost/files/7585706/test_02.log)

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-01-07 00:35:28 UTC  
> Url: https://github.com/boostorg/container/issues/202#issuecomment-1007044611  

Thanks for the report, the bug was fixed in the following commit:  
  
https://github.com/boostorg/container/commit/6b29fbf90df4377d79802747620540864a77c650
