# #21 aligned_alloc: remove old `__MSVCRT_VERSION__` checks for mingw-w64 [Open]

> Username: lazka  
> Created at: 2024-12-31 06:53:57 UTC  
> Updated at: 2024-12-31 07:32:34 UTC  
> Url: https://github.com/boostorg/align/pull/21  

In upstream mingw-w64 the default value of `__MSVCRT_VERSION__` was recently changed from 0x700 to 0x600, causing boost/align to fall back to a mingw-w64 provided _aligned_malloc for pre-winxp targets instead of using the default one. See this for details:  
https://github.com/mingw-w64/mingw-w64/commit/90d3f33f1135b7a9f349267476fe39c27cd96cdc  
  
_aligned_malloc is available in msvcrt.dll since WinXP, according to https://github.com/mingw-w64/mingw-w64/blob/03d8a40f57649fbb773f1cdbe3a760f5e0943e76/mingw-w64-crt/lib-common/msvcrt.def.in#L1175-L1206, so assuming boost/align does not support things older than WinXP, just remove the checks and use one implementation for all of Windows instead.  
  
_aligned_malloc is provided by mingw-w64 itself since 2009, so this change should  
not affect compatibility with any of the currently used mingw-w64 versions.

---
