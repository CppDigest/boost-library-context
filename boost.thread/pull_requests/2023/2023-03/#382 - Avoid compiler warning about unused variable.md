# #382 Avoid compiler warning about unused variable [Closed]

> Username: mojca  
> Created at: 2023-03-16 06:50:18 UTC  
> Updated at: 2026-02-06 11:34:31 UTC  
> Closed at: 2026-02-06 11:34:31 UTC  
> Url: https://github.com/boostorg/thread/pull/382  

When compiling in Visual Studio 2022 with "treat warnings as errors", I'm getting the following error which this PR should address. (The other option is to comment out the name.)  
  
(I would be grateful if a fix could end up in 1.82.0.)  
  
```  
boost\libs\thread\src\win32\thread.cpp(580,236): error C2220: the following warning is treated as an error  
boost\libs\thread\src\win32\thread.cpp(580,236): error C2220:             static inline BOOL WINAPI SetWaitableTimerEx_emulation(HANDLE hTimer, const LARGE_INTEGER *lpDueTime, LONG lPeriod, PTIMERAPCROUTINE pfnCompletionRoutine, LPVOID lpArgToCompletionRoutine, PREASON_CONTEXT WakeContext, ULONG TolerableDelay)  
boost\libs\thread\src\win32\thread.cpp(580,236): error C2220:                                                                                                                                                                                                                                            ^  
boost\libs\thread\src\win32\thread.cpp(580,236): warning C4100: 'TolerableDelay': unreferenced formal parameter  
boost\libs\thread\src\win32\thread.cpp(580,217): warning C4100: 'WakeContext': unreferenced formal parameter  
boost\libs\thread\src\win32\thread.cpp(580,217): warning C4100:             static inline BOOL WINAPI SetWaitableTimerEx_emulation(HANDLE hTimer, const LARGE_INTEGER *lpDueTime, LONG lPeriod, PTIMERAPCROUTINE pfnCompletionRoutine, LPVOID lpArgToCompletionRoutine, PREASON_CONTEXT WakeContext, ULONG TolerableDelay)  
boost\libs\thread\src\win32\thread.cpp(580,217): warning C4100:                                                                                                                                                                                                                         ^  
```

---

## Comment 1

> Username: mojca  
> Created_at: 2023-04-04 09:45:32 UTC  
> Url: https://github.com/boostorg/thread/pull/382#issuecomment-1495667258  

Is there any chance to review/merge this before the next Boost release?

---
