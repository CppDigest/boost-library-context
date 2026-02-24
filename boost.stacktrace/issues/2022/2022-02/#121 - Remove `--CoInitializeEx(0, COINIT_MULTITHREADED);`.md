# #121 - [Windows] Remove `::CoInitializeEx(0, COINIT_MULTITHREADED);` [Closed]

> Username: AlexGuteniev  
> Created at: 2022-02-07 21:34:35 UTC  
> Updated at: 2022-09-01 15:48:28 UTC  
> Closed at: 2022-09-01 14:42:15 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/121  

https://github.com/boostorg/stacktrace/blob/75b7986f9799184ecd679d86273532cb54e6a0dc/include/boost/stacktrace/detail/frame_msvc.ipp#L60  
  
`CoInitializeEx` is superfluous for DbgEng.h interfaces.   
These interfaces are COM-like, not completely COM compliant interfaces, and they don't use COM apartments.  
  
It is now official - see https://github.com/MicrosoftDocs/windows-driver-docs-ddi/pull/1256#issuecomment-1031922715  
  
The potential harm of `::CoInitializeEx(0, COINIT_MULTITHREADED);` is that:  
 * Client code won't be able to initialize COM in `COINIT_APAREMENTTHREADED` mode in the same scope  
 * If client code would try to initialize in `COINIT_APAREMENTTHREADED` mode without checking, this may end up in unbalanced calls

---

## Comment 1

> Username: AlexGuteniev  
> Created at: 2022-02-09 13:42:14 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/121#issuecomment-1033774191  

In the context of #24, probably the COM initializer class shouldn't be totally removed, as DIA SDK is real COM and needs this stuff. Just don't use that for DbgEng

---

## Comment 2

> Username: apolukhin  
> Created at: 2022-09-01 15:48:27 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/121#issuecomment-1234466501  

> In the context of https://github.com/boostorg/stacktrace/issues/24, probably the COM initializer class shouldn't be totally removed, as DIA SDK is real COM and needs this stuff. Just don't use that for DbgEng  
  
I'm not planning to support DIA any time soon, especially due to the it's COM usage.
