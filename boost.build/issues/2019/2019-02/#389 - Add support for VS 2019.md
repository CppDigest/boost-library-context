# #389 - Add support for VS 2019? [Closed]

> Username: pdimov  
> Created at: 2019-02-01 14:35:06 UTC  
> Updated at: 2020-01-24 13:50:21 UTC  
> Closed at: 2019-04-01 18:03:25 UTC  
> Url: https://github.com/boostorg/build/issues/389  

It has officially been announced that VS 2019 will be toolset version v142, cl.exe version 14.2x.xxxx; this should correspond to b2 toolset msvc-14.2.  
  
@mloskot reports that from a 2019 developer command prompt, it already works and is being detected as msvc-14.1.  
  
On a related note, I'm not sure what function `exact_version` is supposed to have here:  
  
https://github.com/boostorg/build/blob/develop/src/tools/msvc.jam#L1178  
  
but it's probably not being set because msvc-14.1 is at version 14.16.xxxx now.

---

## Comment 1

> Username: mloskot  
> Created at: 2019-02-01 15:06:36 UTC  
> Updated at: 2019-02-01 15:07:08 UTC  
> Url: https://github.com/boostorg/build/issues/389#issuecomment-459752941  

FYI, here is of log Boost.MP11 test build with VS2019 Preview 2  
https://gist.github.com/mloskot/4d34a6f6d0e60a543f2ed153af87dcd9

---

## Comment 2

> Username: SpareSimian  
> Created at: 2019-03-10 17:26:15 UTC  
> Url: https://github.com/boostorg/build/issues/389#issuecomment-471325931  

What version should be used for VS 2019? I'm seeing conflicting numbers in the version string, both 14.20 and 19.20:  
  
c:\MSVS16\VC\Tools\MSVC\14.20.27404\bin\Hostx64\x64>cl.exe -?  
Microsoft (R) C/C++ Optimizing Compiler Version 19.20.27404 for x64

---

## Comment 3

> Username: swatanabe  
> Created at: 2019-03-10 17:36:46 UTC  
> Url: https://github.com/boostorg/build/issues/389#issuecomment-471326842  

AMDG  
  
On 3/10/19 11:26 AM, SpareSimian wrote:  
> What version should be used for VS 2019? I'm seeing conflicting numbers in the version string, both 14.20 and 19.20:  
>   
> c:\MSVS16\VC\Tools\MSVC\14.20.27404\bin\Hostx64\x64>cl.exe -?  
> Microsoft (R) C/C++ Optimizing Compiler Version 19.20.27404 for x64  
>   
  
msvc-14.2  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: SpareSimian  
> Created at: 2019-03-11 07:58:18 UTC  
> Url: https://github.com/boostorg/build/issues/389#issuecomment-471438688  

Why 14.2? What's the source for that number? I haven't found it in Microsoft documentation or in the output of the tools.

---

## Comment 5

> Username: mloskot  
> Created at: 2019-03-11 08:53:59 UTC  
> Updated at: 2019-03-11 08:54:19 UTC  
> Url: https://github.com/boostorg/build/issues/389#issuecomment-471453886  

> Why 14.2? What's the source for that number?   
  
The [MSVC Toolset Version](https://devblogs.microsoft.com/cppblog/side-by-side-minor-version-msvc-toolsets-in-visual-studio-2017/)  
  
VS2015 -> v140 (14.00) -> msvc-14.0  
VS2017 -> v141 (14.1x) -> msvc-14.1  
VS2019 -> v142 (14.2x) -> msvc-14.2  
  
where `v142` is the [new toolset in VS2019](https://devblogs.microsoft.com/cppblog/cpp-binary-compatibility-and-pain-free-upgrades-to-visual-studio-2019/)

---

## Comment 6

> Username: swatanabe  
> Created at: 2019-04-01 18:03:25 UTC  
> Url: https://github.com/boostorg/build/issues/389#issuecomment-478682984  

1.70 will have support for VS2019.

---

## Comment 7

> Username: randomcoder91  
> Created at: 2019-06-20 01:21:46 UTC  
> Updated at: 2019-06-20 01:27:38 UTC  
> Url: https://github.com/boostorg/build/issues/389#issuecomment-503803588  

Does this mean boost 1.70 officially supports VS2019 ?.  1.70 docs don't have 14.2 listed as a "primary compiler tested" for VC++.

---

## Comment 8

> Username: jurko-gospodnetic  
> Created at: 2020-01-17 09:14:38 UTC  
> Updated at: 2020-01-17 09:15:59 UTC  
> Url: https://github.com/boostorg/build/issues/389#issuecomment-575541589  

@swatanabe - 1.72 notes still don't list 14.2 as officially supported 😕   
https://www.boost.org/users/history/version_1_72_0.html  
  
and the test suite results at https://www.boost.org/development/tests/master/developer/summary.html don't show any 14.2 toolset version numbers  
  
is that still not officially supported or is that compiler just being recognized by the tool chain as 14.1?

---

## Comment 9

> Username: mloskot  
> Created at: 2020-01-17 10:15:08 UTC  
> Url: https://github.com/boostorg/build/issues/389#issuecomment-575563338  

@jurko-gospodnetic   
>  the test suite results at https://www.boost.org/development/tests/master/developer/summary.html don't show any 14.2 toolset version numbers  
  
This likely means there is no test machine provided (volunteered!) with VS2019 (msvc-14.2) available.  
  
The release notes issue seem to be an overlook. @grafikrobot may know for sure.

---

## Comment 10

> Username: bogdanvso  
> Created at: 2020-01-24 13:50:21 UTC  
> Url: https://github.com/boostorg/build/issues/389#issuecomment-578137129  

So is it correct that it is possible to freely use boost 1.70 and later with MSVC 14.2?
