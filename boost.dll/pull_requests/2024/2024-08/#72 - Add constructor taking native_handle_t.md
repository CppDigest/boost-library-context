# #72 Add constructor taking native_handle_t [Merged]

> Username: tgmatos  
> Created at: 2024-08-25 21:05:49 UTC  
> Updated at: 2024-12-21 08:31:38 UTC  
> Merged at: 2024-12-21 08:30:54 UTC  
> Closed at: 2024-12-21 08:30:54 UTC  
> Url: https://github.com/boostorg/dll/pull/72  

Calls to dlopen() will always fail on FreeBSD if ambient authority is disabled (i.e. process is in capsicum mode). For these cases, FreeBSD offers fdlopen(). The new constructor allows the user to explicitly call fdlopen() and then use Boost.DLL to manage the loaded plugin.  
  
Android's libc (Bionic) also has fdlopen(), but under a different name: android_dlopen_ext().  
  
As Linux implements a sandboxing model closer to Capsicum (that's already planned in the roadmap for the Landlock subsystem), similar restrictions will hit glibc and we might see fdlopen() in glibc as well.  
  
This patch resolves issue #69   
  
(_This patch was made by a friend of mine and I am just submitting it._)

---

## Comment 1

> Username: apolukhin  
> Created_at: 2024-12-21 08:31:37 UTC  
> Url: https://github.com/boostorg/dll/pull/72#issuecomment-2558049690  

Many thanks, merged in https://github.com/boostorg/dll/commit/7ecf45890c7f9d77c88a014cbe9a6bb049683704

---
