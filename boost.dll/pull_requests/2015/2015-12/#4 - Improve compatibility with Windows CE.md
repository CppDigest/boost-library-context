# #4 Improve compatibility with Windows CE [Merged]

> Username: Lastique  
> Created at: 2015-12-28 23:26:03 UTC  
> Updated at: 2015-12-29 21:22:38 UTC  
> Merged at: 2015-12-29 19:57:03 UTC  
> Closed at: 2015-12-29 19:57:03 UTC  
> Url: https://github.com/boostorg/dll/pull/4  

Use winapi::get_proc_address() instead of winapi::GetProcAddress() in order to avoid hitting GetProcAddress macro that is defined on Windows CE and expands to GetProcAddressW.

---
