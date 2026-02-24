# #660 Add ASAN support for MSVC [Merged]

> Username: mikekazakov  
> Created at: 2020-09-24 15:52:42 UTC  
> Updated at: 2021-10-02 20:59:51 UTC  
> Merged at: 2020-12-27 19:35:35 UTC  
> Closed at: 2020-12-27 19:35:35 UTC  
> Url: https://github.com/boostorg/build/pull/660  

Hi,  
  
This change adds Address Sanitizer support for MSVC 16.7+.  
  
The flags try to follow the guidelines from these blogposts:  
https://devblogs.microsoft.com/cppblog/addresssanitizer-asan-for-windows-with-msvc/  
https://devblogs.microsoft.com/cppblog/asan-for-windows-x64-and-debug-build-support/  
  
Various configurations are tested in this repo via GitHub Actions:  
https://github.com/mikekazakov/build-asan-test  
  
The following combinations are tested:  
(Regular / ASAN) x (x86 / x64) x (exe / dll / unit-test) x (static runtime / dynamic runtime).  
  
The Jam code doesn't looks very clean due to the requirement of having different flags for "msvc.link" and "msvc.link.dll". Possibly there's a better way of encoding it, but I haven't found it yet.

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-11-15 02:16:54 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/660#pullrequestreview-530761197  

Seeing as the address sanitizer is only available on recent msvc version it would be better to condition the addition of the options to the versions that support it. Which means adding them in the `configure-version-specific` rule. Can you do those changes?

📁 src/tools/msvc.jam

```diff
1884 |+     # Declare flags for the address sanitizer.
1885 |+     toolset.flags msvc.compile.c OPTIONS <address-sanitizer>on : -fsanitize=address /FS ;
1886 |+     toolset.flags msvc.compile.c++ OPTIONS <address-sanitizer>on : -fsanitize=address /FS ;
```

> Username: grafikrobot  
> Created_at: 2020-11-15 02:14:25 UTC  
> Updated_at: 2020-11-17 11:51:45 UTC  
> Url: https://github.com/boostorg/build/pull/660#discussion_r523630559  

Please use the "/" style options to be consistent with the rest of the options use for msvc.


---

## Comment 2

> Username: mikekazakov  
> Created_at: 2020-11-17 12:25:29 UTC  
> Url: https://github.com/boostorg/build/pull/660#issuecomment-728893378  

I changed the flag prefix to a slash and moved the feature under a version check.  
However, it only tests if the MSVC version is 14.2+ (2019+) as there's no infrastructure to check for minor versions in msvc.jam.  
Now it considers all MSVC 2019 16.0-16.8 versions to be 14.2.

---

## Comment 3

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:50 UTC  
> Url: https://github.com/boostorg/build/pull/660#issuecomment-932819706  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
