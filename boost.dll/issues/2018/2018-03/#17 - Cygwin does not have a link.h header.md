# #17 - Cygwin does not have a link.h header [Closed]

> Username: apolukhin  
> Created at: 2018-03-27 05:31:33 UTC  
> Updated at: 2025-06-11 12:09:16 UTC  
> Closed at: 2025-06-11 12:09:16 UTC  
> Url: https://github.com/boostorg/dll/issues/17  



---

## Comment 1

> Username: Luohaothu  
> Created at: 2025-06-03 10:07:30 UTC  
> Updated at: 2025-06-03 10:19:15 UTC  
> Url: https://github.com/boostorg/dll/issues/17#issuecomment-2934488386  

I've encountered the same issue here and did some research. The root cause seems to be the lack of `dlinfo` implementation for Cygwin and the `HMODULE` handle returned by the Win32 API `LoadLibraryW` is a completely opaque pointer-sized object, which is returned by `dlopen` invocation. Since gcc on Cygwin always implicitly links to `KERNEL32.DLL`, we can hack by calling the corresponding Win32 API `GetModuleFileNameW/A` to get the module file path. I've submitted a PR for this patch, @apolukhin could you please review it?
