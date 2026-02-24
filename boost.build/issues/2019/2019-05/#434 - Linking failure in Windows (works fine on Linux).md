# #434 - Linking failure in Windows (works fine on Linux) [Open]

> Username: djarek  
> Created at: 2019-05-04 12:59:35 UTC  
> Updated at: 2021-06-11 01:55:46 UTC  
> Url: https://github.com/boostorg/build/issues/434  

Having a `run` target that runs an `exe` target (https://github.com/djarek/beast/blob/azp/test/beast/core/Jamfile#L78) works fine on Linux, but fails on Windows because it attempts to link an executable without any object files:   
https://dev.azure.com/damianjarek93/beast/_build/results?buildId=226&view=logs&jobId=7ce3e32e-914e-562e-6fa6-0c87f5911977&taskId=ef6812e9-593a-51df-a19f-70f65eb2c5be&lineStart=715&lineEnd=716&colStart=1&colEnd=1  
```msvc.link bin.v2\libs\beast\test\beast\zlib\run-fat-tests.test\msvc-14.1\debug\address-model-64\cxxstd-17-iso\threadapi-win32\threading-multi\run-fat-tests.exe  
LINK : warning LNK4001: no object files specified; libraries used  
LINK : error LNK2001: unresolved external symbol mainCRTStartup  
bin.v2\libs\beast\test\beast\zlib\run-fat-tests.test\msvc-14.1\debug\address-model-64\cxxstd-17-iso\threadapi-win32\threading-multi\run-fat-tests.exe : fatal error LNK1120: 1 unresolved externals  
```

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:08 UTC  
> Url: https://github.com/boostorg/build/issues/434#issuecomment-859203389  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
