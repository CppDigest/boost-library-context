# #140 Fix build with MinGW-w64 12+. [Merged]

> Username: yh-sb  
> Created at: 2023-08-21 21:28:53 UTC  
> Updated at: 2023-09-02 13:36:37 UTC  
> Merged at: 2023-09-02 13:36:27 UTC  
> Closed at: 2023-09-02 13:36:27 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/140  

For more details see: https://github.com/boostorg/stacktrace/issues/133.  
  
Fixes the following errors with MinGW-w64 12 or higher:  
```  
D:/dev/third_party/boost/libs/stacktrace/include/boost/stacktrace/detail/frame_msvc.ipp:31:5: error: redefinition of 'struct __mingw_uuidof_s<IDebugClient>'  
   31 |     __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
      |     ^~~~~~~~~~~~~~~  
C:/Program Files/mydevtools/MinGW-w64/x86_64-w64-mingw32/include/dbgeng.h:262:1: note: previous definition of 'struct __mingw_uuidof_s<IDebugClient>'  
  262 | __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
      | ^~~~~~~~~~~~~~~  
D:/dev/third_party/boost/libs/stacktrace/include/boost/stacktrace/detail/frame_msvc.ipp:31:5: error: redefinition of 'constexpr const GUID& __mingw_uuidof() [with T = IDebugClient; GUID = GUID]'  
   31 |     __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
      |     ^~~~~~~~~~~~~~~  
C:/Program Files/mydevtools/MinGW-w64/x86_64-w64-mingw32/include/dbgeng.h:262:1: note: 'constexpr const GUID& __mingw_uuidof() [with T = IDebugClient; GUID = GUID]' previously declared here  
  262 | __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
      | ^~~~~~~~~~~~~~~  
D:/dev/third_party/boost/libs/stacktrace/include/boost/stacktrace/detail/frame_msvc.ipp:31:5: error: redefinition of 'constexpr const GUID& __mingw_uuidof() [with T = IDebugClient*; GUID = GUID]'  
   31 |     __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
      |     ^~~~~~~~~~~~~~~  
C:/Program Files/mydevtools/MinGW-w64/x86_64-w64-mingw32/include/dbgeng.h:262:1: note: 'constexpr const GUID& __mingw_uuidof() [with T = IDebugClient*; GUID = GUID]' previously declared here  
  262 | __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
      | ^~~~~~~~~~~~~~~  
D:/dev/third_party/boost/libs/stacktrace/include/boost/stacktrace/detail/frame_msvc.ipp:32:5: error: redefinition of 'struct __mingw_uuidof_s<IDebugControl>'  
   32 |     __CRT_UUID_DECL(IDebugControl,0x5182e668,0x105e,0x416e,0xad,0x92,0x24,0xef,0x80,0x04,0x24,0xba)  
      |     ^~~~~~~~~~~~~~~  
C:/Program Files/mydevtools/MinGW-w64/x86_64-w64-mingw32/include/dbgeng.h:818:1: note: previous definition of 'struct __mingw_uuidof_s<IDebugControl>'  
  818 | __CRT_UUID_DECL(IDebugControl,0x5182e668,0x105e,0x416e,0xad,0x92,0x24,0xef,0x80,0x04,0x24,0xba)  
      | ^~~~~~~~~~~~~~~  
D:/dev/third_party/boost/libs/stacktrace/include/boost/stacktrace/detail/frame_msvc.ipp:32:5: error: redefinition of 'constexpr const GUID& __mingw_uuidof() [with T = IDebugControl; GUID = GUID]'  
   32 |     __CRT_UUID_DECL(IDebugControl,0x5182e668,0x105e,0x416e,0xad,0x92,0x24,0xef,0x80,0x04,0x24,0xba)  
      |     ^~~~~~~~~~~~~~~  
C:/Program Files/mydevtools/MinGW-w64/x86_64-w64-mingw32/include/dbgeng.h:818:1: note: 'constexpr const GUID& __mingw_uuidof() [with T = IDebugControl; GUID = GUID]' previously declared here  
  818 | __CRT_UUID_DECL(IDebugControl,0x5182e668,0x105e,0x416e,0xad,0x92,0x24,0xef,0x80,0x04,0x24,0xba)  
      | ^~~~~~~~~~~~~~~  
...  
```

---

## Review 1 [Commented]

> Username: petrutlucian94  
> Created_at: 2023-08-22 07:57:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/stacktrace/pull/140#pullrequestreview-1588709549  

📁 include/boost/stacktrace/detail/frame_msvc.ipp

```diff
  29 | 
  30 | #ifdef __CRT_UUID_DECL // for __MINGW32__
  31 |+ #if !defined(__MINGW32__) || (__GNUC__ < 12) // https://github.com/boostorg/stacktrace/issues/133
```

> Username: petrutlucian94  
> Created_at: 2023-08-22 07:57:02 UTC  
> Updated_at: 2023-08-22 08:00:45 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/140#discussion_r1301184087  

Thanks for sending a fix!  
  
Can we accommodate mingw-llvm as well? I think we'd have to use something like this:  
  
```  
#if !defined(__MINGW32__) || \  
  ((!defined(__clang__) && __GNUC__ < 12) || \  
   (defined(__clang__) && __clang_major__ < 16))  
```  
  
Here are the macros defined by mingw-llvm 16 (https://github.com/mstorsjo/llvm-mingw):  
  
```  
#define __GNUC_MINOR__ 2  
#define __GNUC_PATCHLEVEL__ 1  
#define __GNUC__ 4  
  
#define __clang_major__ 16  
#define __clang_minor__ 0  
#define __clang_patchlevel__ 0  
```

> Username: yh-sb  
> Created_at: 2023-08-22 08:49:59 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/140#discussion_r1301280419  

Done.


---

## Comment 2

> Username: apolukhin  
> Created_at: 2023-09-02 13:36:37 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/140#issuecomment-1703835443  

Many thanks for the PR!

---
