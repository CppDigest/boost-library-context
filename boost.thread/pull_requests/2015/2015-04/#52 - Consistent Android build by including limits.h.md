# #52  Consistent Android build by including limits.h [Closed]

> Username: PhilLab  
> Created at: 2015-04-15 06:46:42 UTC  
> Updated at: 2015-04-15 15:39:59 UTC  
> Closed at: 2015-04-15 15:39:59 UTC  
> Url: https://github.com/boostorg/thread/pull/52  

Including <limits.h> instead of manually defining PAGE_SIZE.  
  
The old way of including <asm/page.h> was made unavailable since https://android-review.googlesource.com/#/c/83299/ and fixed by defining PAGE_SIZE manually. The question arises whether it would be more consistent to include limits.h which I hereby put up for discussion.

---

## Comment 1

> Username: viboes  
> Created_at: 2015-04-15 11:08:37 UTC  
> Url: https://github.com/boostorg/thread/pull/52#issuecomment-93329746  

thanks. I can accept only PR for the develop branch.

---

## Comment 2

> Username: ned14  
> Created_at: 2015-04-15 11:15:39 UTC  
> Url: https://github.com/boostorg/thread/pull/52#issuecomment-93331802  

Besides I don't think limits.h worked in older Android APIs, which is precisely why we hard code it.  
  
I have no objection to using limits.h on Android 5.0 or later.

---

## Comment 3

> Username: PhilLab  
> Created_at: 2015-04-15 14:12:22 UTC  
> Updated_at: 2015-04-15 14:13:01 UTC  
> Url: https://github.com/boostorg/thread/pull/52#issuecomment-93418915  

When looking in my Android ndk r10d, limits.h exists in all platforms/android-[version]/[architecture]/usr/include so maybe this depends on the NDK version, not the Android version.  
  
Concerning the development branch: ok, I will do so as soon as the "limits.h on older Android APIs" thing is resolved.

---

## Comment 4

> Username: ned14  
> Created_at: 2015-04-15 14:22:19 UTC  
> Url: https://github.com/boostorg/thread/pull/52#issuecomment-93421113  

I believe it is an NDK version thing. Which can be detected using an API version check.  
  
I'll put it another way - no Android device to date doesn't use 4Kb pages, so therefore any which does must use Android 5.0 or later.

---

## Comment 5

> Username: PhilLab  
> Created_at: 2015-04-15 15:39:59 UTC  
> Url: https://github.com/boostorg/thread/pull/52#issuecomment-93454450  

Ok, then we best leave everything as it is before it gets too complicated. After all, it is only a minor thing.

---
