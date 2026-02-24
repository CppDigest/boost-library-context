# #98 Added CMAKE instructions to find boost for MSVC in stage/stage64 [Closed]

> Username: e-fominov  
> Created at: 2016-09-27 13:02:56 UTC  
> Updated at: 2016-09-30 15:27:12 UTC  
> Closed at: 2016-09-30 12:22:07 UTC  
> Url: https://github.com/boostorg/beast/pull/98  

Tested with MinGW 6.2 and MSVC 2015 - both x64 and x86. Boost installed into stage and stage64 folders. Envoronment variable BOOST_ROOT set to boost sources folder  
Boost wat built with commands:  
  
```  
b2 address-model=32 --build-type=minimal stage --stagedir=stage  
b2 address-model=64 --build-type=minimal stage --stagedir=stage64  
```  
  
To build project simply run:  
  
```  
md build  
cd build  
cmake -G"Visual Studio 14 2015"  
cmake --build .  
```  
  
same for Win64. No additional settings needed inside .vcproj files  
  
Boost for MinGW was installed in default c:\boost folder  
If not using environment variable BOOST_ROOT, its possible to add -DBOOST_ROOT=_boost_src_dir_  
  
Also I have removed specific settings for NOT WIN32 in all examples/test and moved them into root CMakeLists.txt

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-09-27 13:15:02 UTC  
> Url: https://github.com/boostorg/beast/pull/98#issuecomment-249861174  

[![Coverage Status](https://coveralls.io/builds/8071291/badge)](https://coveralls.io/builds/8071291)  
  
Coverage remained the same at 97.901% when pulling **071dd791b8bae54afbd4314b07c3d077e88f0a58 on e-fominov:cmake_msvc_findboost** into **658829b0b181ad8b09ede21e7be722ee273aafd4 on vinniefalco:b14**.

---

## Comment 2

> Username: codecov-io  
> Created_at: 2016-09-27 13:15:08 UTC  
> Url: https://github.com/boostorg/beast/pull/98#issuecomment-249861206  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/98?src=pr) is 97.90% (diff: 100%)  
  
> Merging [#98](https://codecov.io/gh/vinniefalco/Beast/pull/98?src=pr) into [b14](https://codecov.io/gh/vinniefalco/Beast/branch/b14?src=pr) will not change coverage  
  
``` diff  
@@                b14        #98   diff @@  
==========================================  
  Files            71         71            
  Lines          4002       4002            
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
  Hits           3918       3918            
  Misses           84         84            
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [658829b...071dd79](https://codecov.io/gh/vinniefalco/Beast/compare/658829b0b181ad8b09ede21e7be722ee273aafd4...071dd791b8bae54afbd4314b07c3d077e88f0a58?src=pr)

---

## Comment 3

> Username: e-fominov  
> Created_at: 2016-09-30 15:24:01 UTC  
> Url: https://github.com/boostorg/beast/pull/98#issuecomment-250773680  

Looks like main branch is much ahead this PR. I can re-open PR for actual master branch. Do you need it?  
And do you want to have "install" target for cmake?

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2016-09-30 15:27:12 UTC  
> Url: https://github.com/boostorg/beast/pull/98#issuecomment-250774524  

I think we should re-open this issue, as a reminder that I need to revisit it later. I can't get to it this second but I agree that this needs to be looked at. I don't have an answers right now.

---
