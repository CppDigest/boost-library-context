# #95 - Boost 1.88.0 -- Visual Studio 2019 compile warning C4456 [Closed]

> Username: dynarithmic  
> Created at: 2025-04-15 19:53:30 UTC  
> Updated at: 2025-10-24 17:06:06 UTC  
> Closed at: 2025-10-24 17:06:06 UTC  
> Url: https://github.com/boostorg/dll/issues/95  

When compiling using Boost 1.88.0 and Visual Studio 2019, the following C4456 warning is emitted in `boost\dll\detail\windows\path_from_handle.hpp`:  
  
```  
boost\dll\detail\windows\path_from_handle.hpp(54,38): warning C4456: declaration of 'size' hides previous local declaration  
boost\dll\detail\windows\path_from_handle.hpp(39,37): message : see declaration of 'size'  
  
```  
Renaming the second `size` variable should remedy the issue.
