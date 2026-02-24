# #305 - How to build boost libraries for arm64 by MSVC on windows [Open]

> Username: PhoebeHui  
> Created at: 2018-05-09 09:45:49 UTC  
> Updated at: 2021-06-11 01:55:58 UTC  
> Url: https://github.com/boostorg/build/issues/305  

Anyone who knows how to build boost libraries for arm64 by MSVC on windows?  
  
The Environment: 64 bit OS + x64-based processor + windows 10 + Visual studio 2017 15.4.2 + boost 1.67.0 + b2  
  
I find a args 'architecture' before and use msvc arm64 compiler, and it seems worked before, however it doesn't have 'arm64' value, and the below command doesn't work anymore.  
  
The command:  
.\bootstrap  
.\b2 headers variant=release --build-dir=..\out\Release_x64 --address-model=64  
.\b2 architecture=arm --variant=release  --toolset=msvc-14.0

---

## Comment 1

> Username: PhoebeHui  
> Created at: 2018-11-28 07:14:20 UTC  
> Url: https://github.com/boostorg/build/issues/305#issuecomment-442343974  

Anyone who know this?

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:20 UTC  
> Url: https://github.com/boostorg/build/issues/305#issuecomment-859203483  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
