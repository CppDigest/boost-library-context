# #11 Don't auto-link when the user requested that we don't [Closed]

> Username: muggenhor  
> Created at: 2015-12-16 14:28:28 UTC  
> Updated at: 2016-09-16 09:34:07 UTC  
> Closed at: 2016-09-16 09:34:07 UTC  
> Url: https://github.com/boostorg/uuid/pull/11  

Don't auto-link when the user requested that we don't

---

## Comment 1

> Username: eldiener  
> Created_at: 2016-09-15 04:16:17 UTC  
> Url: https://github.com/boostorg/uuid/pull/11#issuecomment-247230378  

I am not sure whether or not BOOST_ALL_NO_LIB refers to non-Boost libraries. In this case the advapi32.lib is simply the import library for the Windows advapi32.dll and there is no way to create a library or executable that links with "static" versions of Windows DLLs since they don't exist in that form. So not linking to advapi32.dll cannot be correct AFAICS. Therefore making the fix in the PR, which would not link to advapi32.dll when BOOST_ALL_NO_LIB is defined, seems like it cannot be correct to me.

---

## Comment 2

> Username: muggenhor  
> Created_at: 2016-09-15 07:32:20 UTC  
> Url: https://github.com/boostorg/uuid/pull/11#issuecomment-247257310  

This change isn't about not linking to advapi32. It's about not using the auto-linking feature to do it. By using auto-linking you circumvent your build system and thus make it possible to have incomplete dependency declarations in your build system. Additionally, auto-linking, because of it's circumventing the build system makes it unnecessarily hard to specify which version/variant of a library to link to.  
  
This change gives the user back some control over whether they want to use this feature, or a proper build system instead.

---

## Comment 3

> Username: eldiener  
> Created_at: 2016-09-15 10:46:41 UTC  
> Url: https://github.com/boostorg/uuid/pull/11#issuecomment-247295802  

I do not understand why a programmer in his code should not use auto-linking to a library which cannot be used statically. That's the issue in this particular situation. If we were talking about a library to which you could link to both the static lib and the DLL I could understand your argument. But that is not the case in your PR so I don't understand why you think it should be changed. Since clearly the particular Windows DLL needs to be linked to based on the code in the library I see nothing wrong with doing so automatically when the compiler provides that facility.

---

## Comment 4

> Username: muggenhor  
> Created_at: 2016-09-15 11:35:09 UTC  
> Updated_at: 2016-09-15 11:35:35 UTC  
> Url: https://github.com/boostorg/uuid/pull/11#issuecomment-247304351  

Let me start with this snippet from config/user.hpp:  
  
``` c++  
// BOOST_ALL_NO_LIB: Tells the config system not to automatically select   
// which libraries to link against.    
// Normally if a compiler supports #pragma lib, then the correct library   
// build variant will be automatically selected and linked against,   
// simply by the act of including one of that library's headers.    
// This macro turns that feature off.  
// #define BOOST_ALL_NO_LIB  
```  
  
Which I interpret as meaning it's supposed to allow the user to disable auto-linking.  
  
Then as for why using auto-linking is a problem for our software: it allows omitting dependency declarations from the build system to go through unnoticed (FYI we use CMake). This is a significant problem for us in maintaining the WinCE port of our software because on WinCE advapi32 doesn't exist (its functionality does, but not in the same library).  
  
First of all this means that with auto-linking we get strange linker errors, trying to link to a non-existent library and _no_ single part of our build system (CMake) specifying that library. I.e. it's more difficult than usual to debug because for everything else linker dependencies are controlled by the build system (`target_link_libraries` command).  
  
Secondly, after finding it we now have to keep on linking to this library for regular Windows, but not do so anymore for WinCE. This means some kind of source modification needs to be done (to at least don't link unconditionally to `advapi32`).  
  
Bringing it back under control of our build system (or permitting to do so by using the `#ifdef`) is, for us, the approach that has the least friction in the long term (because auto-linking is a feature almost never encountered meaning it'll take more time than usual to fix errors caused by it). I don't mind if it's not the macro `BOOST_ALL_NO_LIB` controlling this, but it needs to be possible to control this from the compiler command line or boost/config/user.hpp.

---

## Comment 5

> Username: muggenhor  
> Created_at: 2016-09-15 13:41:49 UTC  
> Url: https://github.com/boostorg/uuid/pull/11#issuecomment-247331064  

PS In boostorg/log@a31ec74e517c64c23f59b118f63f4064960a5c96 auto-linking got first optionally disabled and then removed because of these problems. Instead linking is done via the Jamfile there.

---

## Comment 6

> Username: Lastique  
> Created_at: 2016-09-15 16:32:26 UTC  
> Url: https://github.com/boostorg/uuid/pull/11#issuecomment-247380359  

> PS In boostorg/log@a31ec74 auto-linking got first optionally disabled and then removed because of these problems. Instead linking is done via the Jamfile there.  
  
Auto-linking in Boost.Log was broken by the previous commit (merged from https://github.com/boostorg/log/pull/20) because `BOOST_ALL_NO_LIB` is always defined when Boost libraries are built. I removed autolinking because of that and because not all compilers support this feature. It is more portable and consistent to just link all libraries from the Jamfile.  
  
The situation is different in Boost.UUID since it is header-only. The user may request to avoid auto-linking when he uses the library in his code (e.g. if he wants to manage linking himself). That's a valid request, but not honored so far. On the other hand, a compilable Boost library might use Boost.UUID and since `BOOST_ALL_NO_LIB` is defined when it's built it would have unresolved dependencies at link time. Then again, it might be built by a compiler that does not support auto-linking and fail anyway. There is a conflict here, and I'm not sure the solution in this PR is the right one.

---

## Comment 7

> Username: muggenhor  
> Created_at: 2016-09-15 17:20:11 UTC  
> Updated_at: 2016-09-15 17:20:20 UTC  
> Url: https://github.com/boostorg/uuid/pull/11#issuecomment-247393018  

Ah, yes it makes sense that the situation would be different for a header-only library, so they're not exactly comparable.  
  
So it may be that a different option than `BOOST_ALL_NO_LIB` is necessary to inhibit auto-linking. If you have any suggestions for an alternative approach I'd love to hear them.

---

## Comment 8

> Username: eldiener  
> Created_at: 2016-09-15 19:27:32 UTC  
> Updated_at: 2016-09-15 20:13:12 UTC  
> Url: https://github.com/boostorg/uuid/pull/11#issuecomment-247427277  

I can change line 43 in seed_rng.hpp from:  
  
`  
#pragma comment(lib, "advapi32.lib")  
`  
  
to:  
  
`  
#if defined(_WIN32_WCE)  
#pragma comment(lib, "coredll.lib")  
#else  
#pragma comment(lib, "advapi32.lib")  
#endif  
`  
  
This should at least auto-link the correct library on the Windows CE platform

---

## Comment 9

> Username: muggenhor  
> Created_at: 2016-09-15 20:10:11 UTC  
> Updated_at: 2016-09-15 20:10:26 UTC  
> Url: https://github.com/boostorg/uuid/pull/11#issuecomment-247439189  

That would be an improvement. It wouldn't conflict with our build system either I think. Although I'd like to test it to verify.  
  
One thing I just noticed though: according to [Modules/Platform/Windows-MSVC.cmake:168](https://cmake.org/gitweb?p=cmake.git;a=blob;f=Modules/Platform/Windows-MSVC.cmake;h=723c69e644876d230df79179024523aa19a569b1;hb=6e06a53ebf58f41cd570d1bbaed7a1c62eac7ed0#l168) CMake, by default, already links with that library:  
  
``` cmake  
set(CMAKE_C_STANDARD_LIBRARIES_INIT "coredll.lib ole32.lib oleaut32.lib uuid.lib commctrl.lib")  
```  
  
It seems to contain just about the entire Windows API as well, so it wouldn't surprise me if this is the default list used by other build systems (notably MSVC itself) as a default as well. But I doubt it'll hurt to explicitly link to that.  
  
I'll try to get access to a WinCE build system tomorrow or bother a colleague to test your proposed solution. (The patch contained by this PR is working for us btw).

---

## Comment 10

> Username: eldiener  
> Created_at: 2016-09-15 20:17:28 UTC  
> Url: https://github.com/boostorg/uuid/pull/11#issuecomment-247441503  

I will make the change I suggested on the 'develop' branch and you can test it with your WinCE port. As to this PR I think that it may break other libraries, as has been suggested and therefore I am reticent to approve of it.

---

## Comment 11

> Username: muggenhor  
> Created_at: 2016-09-15 20:22:38 UTC  
> Url: https://github.com/boostorg/uuid/pull/11#issuecomment-247443184  

That's completely understandable, I just wasn't aware that `BOOST_ALL_NO_LIB` might have a different meaning for header-only libraries. So if this works I'm happy with it.

---

## Comment 12

> Username: eldiener  
> Created_at: 2016-09-15 21:03:35 UTC  
> Url: https://github.com/boostorg/uuid/pull/11#issuecomment-247454894  

I pushed a fix on 'develop' that auto-links the advapi32 library under Windows and the coredll library under Win CE. I know that is not what you wanted but until this issue is fully resolved or decided upon, that is the best I felt responsible for doing now.  
  
Unfortunately my 'develop' change renders your current PR as conflicting with what is now in the 'develop' branch.

---

## Comment 13

> Username: muggenhor  
> Created_at: 2016-09-16 09:34:07 UTC  
> Url: https://github.com/boostorg/uuid/pull/11#issuecomment-247558543  

@eldiener we're still on 1.59.0, so I cherry-picked your commit on top of that release and tested that version (just for reference I pushed the cherry-picked version to this PR as boostorg/uuid@43f871234ebb6771167e953d94dc7132ac504ee3).  
  
It builds perfectly. I still think there's a valid use case for disabling auto-linking altogether on user-request, but I don't need it right now. Thanks.

---
