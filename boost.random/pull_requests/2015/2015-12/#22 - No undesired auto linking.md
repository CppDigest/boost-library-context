# #22 No undesired auto linking [Closed]

> Username: muggenhor  
> Created at: 2015-12-16 14:16:31 UTC  
> Updated at: 2023-05-14 11:15:28 UTC  
> Closed at: 2023-05-13 17:41:01 UTC  
> Url: https://github.com/boostorg/random/pull/22  

Don't auto-link when the user requested that we don't

---

## Comment 1

> Username: swatanabe  
> Created_at: 2023-05-13 17:41:01 UTC  
> Url: https://github.com/boostorg/random/pull/22#issuecomment-1546717709  

This is definitely the wrong solution. The user doesn't (usually) control the build of the random library.

---

## Comment 2

> Username: muggenhor  
> Created_at: 2023-05-14 11:15:28 UTC  
> Url: https://github.com/boostorg/random/pull/22#issuecomment-1546874878  

FYI: this was a long long time ago. But what i remember is that this change was necessary for building and using this with WinCE. In that scenario we did control the build.  
  
I don't recall the exact problem but WinCE had a lot of it's functions in different libraries from the desktop variant. So possibly Advapi32 didn't even exist there. It's use definitely caused build errors.  
  
But it's a long time ago, and the project in question is in maintenance by completely different people now. So this is the only context I can provide from memory now

---
