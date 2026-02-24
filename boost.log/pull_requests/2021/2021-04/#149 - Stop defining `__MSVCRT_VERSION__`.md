# #149 Stop defining `__MSVCRT_VERSION__` [Merged]

> Username: lazka  
> Created at: 2021-04-05 15:30:53 UTC  
> Updated at: 2021-04-06 07:20:17 UTC  
> Merged at: 2021-04-05 21:38:52 UTC  
> Closed at: 2021-04-05 21:38:52 UTC  
> Url: https://github.com/boostorg/log/pull/149  

`__MSVCRT_VERSION__` is a mingw internal and shouldn't be set by the user.  
  
Setting it to something different than the toolchain provided value means the  
headers and crt wont match which can result in crashes and build errors.  
  
This fixes the boost build when targeting ucrt.  
  
Downstream issue for context: https://github.com/msys2/MINGW-packages/issues/8283

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-04-05 15:40:28 UTC  
> Url: https://github.com/boostorg/log/pull/149#issuecomment-813460481  

As far as I remember, this macro was required to make some C runtime functions available, I don't remember which. This macro was not defined by the compiler; at best it was defined by the C headers. Has something changed in this regard in MinGW?

---

## Comment 2

> Username: lazka  
> Created_at: 2021-04-05 15:49:03 UTC  
> Url: https://github.com/boostorg/log/pull/149#issuecomment-813465092  

It's defined by the mingw-w64 headers, and the default has been `0x0700` for 13 years as far as I can see unless targeting a newer crt: https://github.com/mirror/mingw-w64/blob/2afb89e6a651f9c5749d9de492a263e03ba1e94e/mingw-w64-headers/configure.ac#L170  
  
imo if some non-optional functions weren't available without this then the toolchain was just targeting a tool old crt version and the build should have errored out instead.

---

## Comment 3

> Username: lazka  
> Created_at: 2021-04-05 16:06:21 UTC  
> Updated_at: 2021-04-05 16:09:20 UTC  
> Url: https://github.com/boostorg/log/pull/149#issuecomment-813474523  

Looking through the mingw-w64 git history it indeed seems like one could set `__MSVCRT_VERSION__` to target a newer version before they started supporting multiple crts, so this code might have made sense at that time (>5 years ago).  
  
But the default has been `0x0700` for 13 years now. So removing this shouldn't even break very old mingw setups, while unbreaking new ones.

---

## Comment 4

> Username: Lastique  
> Created_at: 2021-04-05 16:13:39 UTC  
> Url: https://github.com/boostorg/log/pull/149#issuecomment-813478613  

This code applies not only to MinGW-w64, but also to the legacy MinGW. Which I don't think has changed much in the recent years, did it? Is this change also intended for the legacy MinGW?

---

## Comment 5

> Username: lazka  
> Created_at: 2021-04-05 16:37:58 UTC  
> Updated_at: 2021-04-05 17:28:00 UTC  
> Url: https://github.com/boostorg/log/pull/149#issuecomment-813491974  

I've never used legacy mingw I'm afraid.  
  
I've found the code here: https://osdn.net/projects/mingw/scm/git/mingw-org-wsl/blobs/5.4-trunk/mingwrt/include/msvcrtver.h  
  
```  
#ifndef __MSVCRT_VERSION__  
/* This may be set, when the intent is to link with any of the above  
 * non-freely distributable MSVCRxx.DLL libraries, rather than with the  
 * pseudo-free MSVCRT.DLL provided as an OS component.  High byte is the  
 * major version number, low byte is the minor; however, users are advised  
 * to use custom GCC specs files to set this, while also substituting the  
 * appropriate library in place of MSVCRT.DLL, rather than to simply set  
 * it directly.  
 *  
 * It should be noted that __MSVCRT_VERSION__ is NOT a good indicator of  
 * evolving MSVCRT.DLL features; that is better accomplished by using the  
 * NTDDI_VERSION setting from the Windows API.  Thus, users of MSVCRT.DLL  
 * should NOT set __MSVCRT_VERSION__, leaving us to establish a default,  
 * equivalent to MSVCR60.DLL, which seems reasonably well aligned with  
 * the feature set of the earliest MSVCRT.DLL version we support.  
 */  
# define __MSVCRT_VERSION__  __MSVCR60_DLL  
#endif  
```  
  
which indicates that indeed things might be broken there by default. But I see that in many places in mingw32 it checks for `_WIN32_WINNT` as well, and that is set to `0x0500` in boost, which should imply `__MSVCR70_DLL`  
  
Let's see if CI goes through.

---

## Comment 6

> Username: mstorsjo  
> Created_at: 2021-04-05 19:52:50 UTC  
> Url: https://github.com/boostorg/log/pull/149#issuecomment-813612800  

FWIW, mingw.org headers used to check this define for hiding some declarations (in particular, `_aligned_malloc` comes to mind). As mingw-w64 headers (used to) default to XP or newer, that function has alwasy been available there anyway, but with mingw.org headers, people have been setting the define to be able to use that version:  
  
https://osdn.net/projects/mingw/scm/git/mingw-org-wsl/blobs/5.4-trunk/mingwrt/include/malloc.h  
  
```  
#if __MSVCRT_VERSION__ >= __MSVCR70_DLL  
/* First introduced in non-free MSVCR70.DLL, the following were subsequently  
 * made available from MSVCRT.DLL, from the release of WinXP onwards; however,  
 * we choose to declare them only for the non-free case, preferring to emulate  
 * them, in terms of libmingwex.a replacement implementations, for consistent  
 * behaviour across ALL MSVCRT.DLL versions.  
 */  
_CRTIMP __cdecl __MINGW_NOTHROW __MINGW_ATTRIB_MALLOC  
void *_aligned_malloc (size_t, size_t);  
```

---

## Comment 7

> Username: Lastique  
> Created_at: 2021-04-05 21:38:42 UTC  
> Url: https://github.com/boostorg/log/pull/149#issuecomment-813666147  

I would prefer to keep the definition just for legacy MinGW, but it seems there is no way to tell MinGW and MinGW-w64 apart without including a header. Which will define `__MSVCRT_VERSION__` and defeat the purpose. So it looks like I'll have to just remove it altogether.  
  
CI build passed, so the runtime functions enabled by this macro are probably no longer used.  
  
Thanks for the report and the PR.

---

## Comment 8

> Username: lazka  
> Created_at: 2021-04-06 07:20:17 UTC  
> Url: https://github.com/boostorg/log/pull/149#issuecomment-813889255  

Thanks!

---
