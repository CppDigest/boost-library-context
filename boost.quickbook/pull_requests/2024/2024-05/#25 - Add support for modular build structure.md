# #25 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-05-01 15:41:36 UTC  
> Updated at: 2025-05-03 13:22:15 UTC  
> Merged at: 2025-05-02 16:02:30 UTC  
> Closed at: 2025-05-02 16:02:30 UTC  
> Url: https://github.com/boostorg/quickbook/pull/25  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-04-14 14:12:12 UTC  
> Url: https://github.com/boostorg/quickbook/pull/25#issuecomment-2801853288  

Unnecessary CI changes. Also, it's a bit odd for build/Jamfile to be deleted.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2025-04-15 00:24:30 UTC  
> Url: https://github.com/boostorg/quickbook/pull/25#issuecomment-2803408292  

> Unnecessary CI changes.   
  
Undone. But a bunch of other CI changes to make things actually work.  
  
> Also, it's a bit odd for build/Jamfile to be deleted.  
  
True. But it essentially did nothing anyway.

---

## Comment 3

> Username: sdarwin  
> Created_at: 2025-05-02 23:00:12 UTC  
> Url: https://github.com/boostorg/quickbook/pull/25#issuecomment-2848246065  

Circleci boost builds are failing, it seems to correspond with this quickbook PR merge today.  
  
maybe not important, but there was a switch from "cxxstd-0x-iso release" to "cxxstd-11-iso debug".  
  
log file line 113, before:    
  
```  
[1] gcc-13  
[2] gcc-13/release/x86_64/cxxstd-0x-iso/link-static/visibility-hidden  
# /root/project> 'b2' '--debug-configuration' '-j3' '-q' '-d0' '--build-dir=/root/build' '--distdir=/root/build/dist' 'tools/quickbook' '' execution time 60.259230852127075 seconds  
```  
  
log file line 113, after:  
  
```  
[1] gcc-13  
[2] gcc-13/debug/x86_64/cxxstd-11-iso/link-static/visibility-hidden  
# /root/project> 'b2' '--debug-configuration' '-j3' '-q' '-d0' '--build-dir=/root/build' '--distdir=/root/build/dist' 'tools/quickbook' '' execution time 49.32090091705322 seconds  
```

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-05-02 23:39:28 UTC  
> Url: https://github.com/boostorg/quickbook/pull/25#issuecomment-2848279849  

From the Circle log, it appears like building the Quickbook documentation itself hangs, for some reason. But I can't reproduce this here.

---

## Comment 5

> Username: sdarwin  
> Created_at: 2025-05-03 11:27:34 UTC  
> Url: https://github.com/boostorg/quickbook/pull/25#issuecomment-2848580152  

> Add back release to the quickbook target  
  
This may apply to `b2` or `quickbook` , but the log output line 113 still says debug instead of release.    
  
gcc-13/debug/x86_64/cxxstd-11-iso/link-static/visibility-hidden

---

## Comment 6

> Username: pdimov  
> Created_at: 2025-05-03 13:03:01 UTC  
> Url: https://github.com/boostorg/quickbook/pull/25#issuecomment-2848619408  

Looks like debug is being built, yes. But there's nothing in the log because of -d0. Which script was that, again?

---

## Comment 7

> Username: pdimov  
> Created_at: 2025-05-03 13:07:14 UTC  
> Url: https://github.com/boostorg/quickbook/pull/25#issuecomment-2848621028  

OK, let's see how that would go. https://github.com/boostorg/release-tools/commit/fd465dfe9aad885117502f0b7a4534176f67d444

---

## Comment 8

> Username: pdimov  
> Created_at: 2025-05-03 13:08:57 UTC  
> Url: https://github.com/boostorg/quickbook/pull/25#issuecomment-2848621664  

I can confirm that building `tools/quickbook` installs the debug build.  
  
```  
C:\boost-git\develop\tools\quickbook>b2 .  
Performing configuration checks  
  
...  
  
[1] msvc-14.3  
[2] msvc-14.3/debug/x86_64/cxxstd-11-iso/link-static/python-3.9/threadapi-win32/threading-multi/visibility-hidden  
...patience...  
...patience...  
...found 5407 targets...  
...updating 1 target...  
common.copy C:\boost-git\develop\dist\bin\quickbook.exe  
..\..\bin.v2\tools\quickbook\src\msvc-14.3\debug\x86_64\cxxstd-11-iso\link-static\threading-multi\quickbook.exe  
        1 file(s) copied.  
  
...updated 1 target...  
```

---

## Comment 9

> Username: pdimov  
> Created_at: 2025-05-03 13:22:13 UTC  
> Url: https://github.com/boostorg/quickbook/pull/25#issuecomment-2848626705  

I have no idea how `default-build` is supposed to work in this case, what's inherited from what and how `<cxxstd>11` and `<variant>release` are combined. But in either case, this change https://github.com/boostorg/quickbook/commit/fea2283c87e59d6c9d8f12692f0c39987076c56f seems to fix it for me locally, that is, building `tools/quickbook` now installs the release build.  
  
An easier change would have been to just put `<variant>release` into the project `default-build`, and maybe that's the right one. I went with the above because it's closer to what the original did.

---
