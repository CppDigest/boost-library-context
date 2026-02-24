# #140 - [Windows] Require WinXP on Cygwin [Closed]

> Username: Flamefire  
> Created at: 2020-04-24 16:29:37 UTC  
> Updated at: 2020-05-02 17:20:27 UTC  
> Closed at: 2020-05-02 17:20:27 UTC  
> Url: https://github.com/boostorg/filesystem/issues/140  

@Lastique https://github.com/boostorg/filesystem/commit/c758552338e40ffe4c0bde127a81a753a3366fa7#diff-ead8a914c233d0bd0932e25b4ea0318a introduced a potential regression. Previously WinXP API level was required on Cygwin and MinGW by this code:  
  
```  
#   if (defined(__MINGW32__) || defined(__CYGWIN__)) && !defined(WINVER)  
      // Versions of MinGW or Cygwin that support Filesystem V3 support at least WINVER 0x501.  
      // See MinGW's windef.h  
#     define WINVER 0x501  
#   endif  
```  
  
But due to the change this now defaults to 0x500, see test here https://ci.appveyor.com/project/Lastique/filesystem/builds/32419901/job/yb3wknac4qh82n4h where I print the values before and after inclusion of the WinAPI header. I'm inclined to say that this change is not intended  
  
I noticed it as symlink support is possible at API level 0x501 and I tried to use it in https://github.com/boostorg/filesystem/pull/100 but tests fail due to the (now only) check for the API level detecting that on cygwin symlinks are not supported (but they are)  
  
What would be your preferred resolution?

---

## Comment 1

> Username: Lastique  
> Created at: 2020-04-24 16:41:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/140#issuecomment-619121796  

In general, I would prefer to use whatever default is chosen by Boost.WinAPI or the user via command line. Boost.Filesystem should not define any particular target Windows version, unless it requires it as a minimum. Currently, to my knowledge, Boost.Filesystem is compatible with any target Windows versions supported by Boost.WinAPI.  
  
Regarding symlink support, I believe, it should be optional and not compiled when target Windows version does not support it. And as far as I remember, Windows 2000 (0x0500) did support them, although possibly not to the degree of Windows XP (0x0501) and later. I vaguely remember that I used junction.exe on Windows 2000 back when I used that system. That said, I really don't know how symlink support is represented and how well is supported in various Windows SDKs.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-04-24 16:42:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/140#issuecomment-619122610  

> Regarding symlink support, I believe, it should be optional and not compiled when target Windows version does not support it.  
  
Alternatively, it could be detected at run time via `GetProcAddress` and friends.

---

## Comment 3

> Username: Flamefire  
> Created at: 2020-04-24 16:43:47 UTC  
> Url: https://github.com/boostorg/filesystem/issues/140#issuecomment-619123225  

I'll write the check with 0x500 and see what happens. Symlink creating support is already enabled at runtime as suggested in #100

---

## Comment 4

> Username: Flamefire  
> Created at: 2020-04-25 13:41:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/140#issuecomment-619381415  

> Alternatively, it could be detected at run time via GetProcAddress and friends.  
  
Factored this out into #142. Will rebase #100 onto that once that is merged.
