# #427 - Spectre mitigation warning on progress_monitor code [Open]

> Username: correaa  
> Created at: 2024-07-23 21:26:45 UTC  
> Updated at: 2024-07-23 21:30:55 UTC  
> Url: https://github.com/boostorg/test/issues/427  

I am using Boost.Test (in header-only mode, using "included"), for a Boost-aspiring project https://github.com/correaa/boost-multi  
  
I am getting this warning with MSVC 143:  
  
```  
D:\a\boost-multi\boost-multi\vcpkg\installed\x64-windows\include\boost\test\impl\progress_monitor.ipp(62): warning C5045: Compiler will insert Spectre mitigation for memory load if /Qspectre switch specified [D:\a\boost-multi\boost-  
```  
https://github.com/correaa/boost-multi/actions/runs/10066511703/job/27828050365#step:9:62  
  
FWIIW, this line has suspicious code as a variable is modified inside a condition: https://github.com/boostorg/test/blob/develop/include/boost/test/impl/progress_monitor.ipp#L61
