# #111 - Minor regression with toolset v141_xp (Visual Studio 2017 - Windows XP) [Closed]

> Username: thebrandre  
> Created at: 2023-08-16 14:41:08 UTC  
> Updated at: 2023-09-05 14:01:09 UTC  
> Closed at: 2023-09-05 14:01:08 UTC  
> Url: https://github.com/boostorg/system/issues/111  

Before boost version 1.83 and commit 32dbf1b99290ced5666f6b1e8390571c02c94c5e it was possible to compile for Windows XP by setting just the one define  `define=BOOST_USE_WINAPI_VERSION=0x0501` as an argument for **b2**.  
  
Now it is also required to add `define=_USING_V110_SDK71_`. Otherwise you'll get a linker error:  
  
> msvcprt.lib(sharedmutex.obj) : error LNK2019: unresolved external symbol __imp_ReleaseSRWLockExclusive referenced in function _Smtx_unlock_exclusive  
> msvcprt.lib(sharedmutex.obj) : error LNK2019: unresolved external symbol __imp_ReleaseSRWLockShared referenced in function _Smtx_unlock_shared  
> msvcprt.lib(sharedmutex.obj) : error LNK2019: unresolved external symbol __imp_AcquireSRWLockExclusive referenced in function _Smtx_lock_exclusive  
> msvcprt.lib(sharedmutex.obj) : error LNK2019: unresolved external symbol __imp_AcquireSRWLockShared referenced in function _Smtx_lock_shared  
> msvcprt.lib(sharedmutex.obj) : error LNK2019: unresolved external symbol __imp_TryAcquireSRWLockExclusive referenced in function _Smtx_try_lock_exclusive  
> msvcprt.lib(sharedmutex.obj) : error LNK2019: unresolved external symbol __imp_TryAcquireSRWLockShared referenced in function _Smtx_try_lock_shared  
  
It is not a big deal because we should have been adding this define all along. So there is not actually a need for action here and you can simply close the issue.  
I just wanted to point it out in case anyone has a similar problem because, unfortunately, this was not quite so easy to trace back.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-09-05 14:01:09 UTC  
> Url: https://github.com/boostorg/system/issues/111#issuecomment-1706683536  

A similar problem has been reported in https://github.com/boostorg/system/issues/113 and the fix https://github.com/boostorg/system/commit/a57c5683243ceacc58ee5e3bdc806f189df44dac should make this no longer an issue, even though defining `_USING_V110_SDK71_` is still a good idea.
