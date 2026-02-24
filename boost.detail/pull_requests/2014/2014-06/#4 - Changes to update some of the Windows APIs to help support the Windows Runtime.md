# #4 [winrt support] Changes to update some of the Windows APIs to help support the Windows Runtime [Merged]

> Username: stgates  
> Created at: 2014-06-02 19:23:51 UTC  
> Updated at: 2014-06-14 00:50:30 UTC  
> Merged at: 2014-06-03 19:32:20 UTC  
> Closed at: 2014-06-03 19:32:20 UTC  
> Url: https://github.com/boostorg/detail/pull/4  

These changes update some of the Windows APIs to newer versions when targeting later versions of Windows (for example GetSystemInfo/GetNativeSystemInfo). Some APIs that aren't available in the Windows Runtime are excluded unless targeting Windows desktop.

---
