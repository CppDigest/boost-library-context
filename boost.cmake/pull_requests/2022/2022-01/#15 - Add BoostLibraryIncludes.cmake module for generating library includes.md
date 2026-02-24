# #15 Add BoostLibraryIncludes.cmake module for generating library includes [Closed]

> Username: Lastique  
> Created at: 2022-01-15 13:14:45 UTC  
> Updated at: 2022-02-04 17:09:54 UTC  
> Closed at: 2022-02-03 20:30:45 UTC  
> Url: https://github.com/boostorg/cmake/pull/15  

This module sets `BOOST_LIBRARY_INCLUDES` variable to the list of all library includes. The module detects the monolithic include directory, which is present in the official monolithic Boost distribution and after `b2 headers`, and returns it instead when present.  
  
This module is useful when implementing CMake configuration checks that rely on Boost libraries. Configuration checks cannot use CMake targets defined by libraries because at the point of configuration these targets are not yet defined.  
  
This module basically extracts the duplicate code that is present in multiple Boost libraries I maintain. Once it's merged, I will be able to switch these libraries to this common implementation. Plus, this will avoid scanning the filesystem multiple times, slightly reducing configuration time.

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-02-03 20:05:28 UTC  
> Url: https://github.com/boostorg/cmake/pull/15#issuecomment-1029354912  

I haven't forgotten about this either. The reason I don't like it, apart from the general feeling that fs scan isn't right in principle, is that if applied, it will break the use of your libraries without a superproject, and that's a scenario I would like to support (both using git submodules or FetchContent.)  
  
E.g. https://github.com/pdimov/boost_cmake_demo. This will break first because there's no `tools/cmake` and therefore the common module won't be found at all, and then for more subtle reasons such as the first directory scan picking up some partial state and remembering it in the cache variable, and then later scans not being performed even if more directories have appeared as a result of FetchContent.  
  
There's also the problem of Config not necessarily being present when Atomic is fetched and configured, but this can be pushed onto the user by mandating a correct fetch order (even though, ideally, we shouldn't) and is a general issue that is unrelated to this PR.

---

## Comment 2

> Username: Lastique  
> Created_at: 2022-02-03 20:30:45 UTC  
> Url: https://github.com/boostorg/cmake/pull/15#issuecomment-1029374219  

I'm not going to support FetchContent, period, and git submodules are out of scope of a build system, so that is not a problem.  
  
Building without a superproject or `tools/cmake` might be a valid use case in the future, if Boost becomes truly modular (i.e. if each library is distributed in its own package), but this means we have no way to move some common CMake code to some shared place. I would think, even in a fully modular world there would be a way to express requirement on `tools/cmake` for a library.  
  
Anyway, I guess I'll have to keep this code duplicated in libraries. Thanks for the reply.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-02-03 23:30:52 UTC  
> Url: https://github.com/boostorg/cmake/pull/15#issuecomment-1029498671  

Everything supports at the moment the FetchContent or add_subdirectory workflows (without a superproject), so it will be a regression to make the libraries not support them.

---

## Comment 4

> Username: Lastique  
> Created_at: 2022-02-04 08:19:17 UTC  
> Url: https://github.com/boostorg/cmake/pull/15#issuecomment-1029750418  

At least Boost.Atomic, Boost.Filesystem and Boost.Log rely on Boost tree in the filesystem at configure time, so these libraries are incompatible with FetchContent, if I understand it right.

---

## Comment 5

> Username: pdimov  
> Created_at: 2022-02-04 14:42:43 UTC  
> Url: https://github.com/boostorg/cmake/pull/15#issuecomment-1030051027  

Boost.Atomic only needs Config, so it should work if Config is fetched before it. It currently doesn't rely on the entire Boost structure, it just enumerates `..` for sibling directories, and this will work (another reason why this PR would have been a regression as it hardcodes a dependency on the Boost directory structure.) Filesystem needs Atomic I think for its checks? This would also work if Atomic and Config are fetched before it.  
  
The reason I didn't reply for so long was that I wanted to setup projects demonstrating this, at least for Atomic, but it seems I was never able to find the time.  
  
Now in principle, I think that the configure checks should always be self-contained and not rely on another Boost library being present, but I don't even follow this rule myself. :-)

---

## Comment 6

> Username: Lastique  
> Created_at: 2022-02-04 14:59:01 UTC  
> Updated_at: 2022-02-04 15:00:03 UTC  
> Url: https://github.com/boostorg/cmake/pull/15#issuecomment-1030065232  

> Boost.Atomic only needs Config  
  
And Predef and WinAPI.  
  
> Filesystem needs Atomic I think for its checks?  
  
Also Predef and WinAPI.  
  
> This would also work if Atomic and Config are fetched before it.  
  
Yes, probably. I must admit I don't use FetchContent and probably don't fully understand how it works. If the user (or superproject, or whoever else is willing to use FetchContent) is ok to list libraries in the order that works then that's fine - it is his responsibility to do this correctly. As far as libraries I maintain are concerned - all needed libraries must be present in the filesystem when configuration step is started. The setup when that is not the case is not supported.

---

## Comment 7

> Username: pdimov  
> Created_at: 2022-02-04 15:17:34 UTC  
> Url: https://github.com/boostorg/cmake/pull/15#issuecomment-1030081701  

> And Predef and WinAPI.  
  
Ah right. I now remember looking at the config checks, and thinking whether you actually need these there. You can just include `<windows.h>` in the config check.

---

## Comment 8

> Username: Lastique  
> Created_at: 2022-02-04 17:09:54 UTC  
> Url: https://github.com/boostorg/cmake/pull/15#issuecomment-1030180836  

> You can just include `<windows.h>` in the config check.  
  
I want macros defined by Boost.WinAPI to take effect.

---
