# #100 - Fails to link when using clang compiler under Visual Studio [Closed]

> Username: MomoDeve  
> Created at: 2020-10-09 11:21:33 UTC  
> Updated at: 2024-09-12 08:15:08 UTC  
> Closed at: 2024-09-12 08:15:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/100  

```  
D:\repos\MxEngine\out\build\x64-Clang-Debug\lld-link : error : undefined symbol: DebugCreate  
  >>> referenced by D:\boost\boost\stacktrace\detail\frame_msvc.ipp:126  
  >>>               MxEngine.lib(Platform.cpp.obj):(private: static void __cdecl boost::stacktrace::detail::debugging_symbols::try_init_com(class boost::stacktrace::detail::com_holder<struct IDebugSymbols> &, class boost::stacktrace::detail::com_global_initer const &))  
```  
Fixed by manually including winapi libraries:  
```cpp  
#if defined(WIN32)  
    #pragma comment(lib, "ole32.lib")  
    #pragma comment(lib, "Dbgeng.lib")  
#endif  
#include <boost/stacktrace.hpp>  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-11-18 19:52:27 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/100#issuecomment-729916206  

AFAIK MinGW* and cygwin compilers do not have auto linking support, so `#if defined(WIN32)` won't work right all the time.   
  
Is there some macro to uniquely identify clang usage on visual studio?

---

## Comment 2

> Username: MomoDeve  
> Created at: 2020-11-18 20:26:52 UTC  
> Updated at: 2020-11-18 20:28:05 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/100#issuecomment-729933967  

I havent found macro exactly for that, me myself checking it by writing   
`#if defined(__clang__) && defined(_WIN32)`  
Instead of `_WIN32` there could also be `_MSC_VER`, clang sets it for compatibilty with msvc compiler if it was launched using Visual Studio toolchain
