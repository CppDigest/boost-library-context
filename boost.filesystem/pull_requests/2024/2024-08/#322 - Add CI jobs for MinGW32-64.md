# #322 Add CI jobs for MinGW32/64 [Merged]

> Username: Flamefire  
> Created at: 2024-08-25 15:44:27 UTC  
> Updated at: 2024-08-26 11:14:55 UTC  
> Merged at: 2024-08-26 11:13:04 UTC  
> Closed at: 2024-08-26 11:13:04 UTC  
> Url: https://github.com/boostorg/filesystem/pull/322  

Add jobs using MSYS2 MinGW to GHA  
  
Config copied from boost-ci  
  
This is the configuration where I see #321 although it seems to be only triggered by `-Wextra` not `-Wall`: https://godbolt.org/z/3fqjG7zM3

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-08-25 15:57:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/322#issuecomment-2308905365  

- C++03 is not supported  
- no `actions/checkout`, please. It causes too much trouble.  
- no `boost-ci`, please. I would like to know what and how I'm testing.  
- There already is a [MinGW-w64 job](https://github.com/boostorg/filesystem/blob/5dc58be3cddaa00497f9fd95deb8766bb91e2f3a/.github/workflows/ci.yml#L563-L565), why do we need another one?  
- I'm not sure what MINGW32 is, but if this is the legacy MinGW32 (as opposed to MinGW-w64) then it is no longer supported. I think, it's been dropped by Boost.System as well.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2024-08-25 16:14:39 UTC  
> Url: https://github.com/boostorg/filesystem/pull/322#issuecomment-2308910696  

> * C++03 is not supported  
  
So the build should be skipped by https://github.com/boostorg/filesystem/blob/5dc58be3cddaa00497f9fd95deb8766bb91e2f3a/build/Jamfile.v2#L126-L137  
  
If you consider it not worth ensuring this skipping works in this specific configuration I can remove that.  
  
>     * no `actions/checkout`, please. It causes too much trouble.  
>   
>     * no `boost-ci`, please. I would like to know what and how I'm testing.  
  
I can C&P the steps from the posix jobs if you prefer that. I used boost-ci to reduce repetition  
  
>     * There already is a [MinGW-w64 job](https://github.com/boostorg/filesystem/blob/5dc58be3cddaa00497f9fd95deb8766bb91e2f3a/.github/workflows/ci.yml#L563-L565), why do we need another one?  
>   
>     * I'm not sure what MINGW32 is, but if this is the legacy MinGW32 (as opposed to MinGW-w64) then it is no longer supported. I think, it's been dropped by Boost.System as well.  
  
The ones added here use the MSYS2 MinGW-w64 distros for 32 & 64 bit. On a Windows machine those are called "MSYS2 MinGW x86" and "MSYS2 MinGW x64" respectively. The reason for using MSYS2 is (to quote from their website):  
  
> MSYS2 provides up-to-date native builds for GCC, mingw-w64 [...]  
  
--> The currently tested GCC is GCC-8, MSYS2 uses GCC-13. So this adds testing for recent GCCs on Windows

---

## Comment 3

> Username: Lastique  
> Created_at: 2024-08-25 16:28:56 UTC  
> Url: https://github.com/boostorg/filesystem/pull/322#issuecomment-2308915446  

> If you consider it not worth ensuring this skipping works in this specific configuration I can remove that.  
  
I see no point in running a C++03 CI build when it is not expected to work (whether with an error or silently skip the build is not important).  
  
> I can C&P the steps from the posix jobs if you prefer that.  
  
Yes, that would be ok, if those steps work on MSYS2.  
  
> The ones added here use the MSYS2 MinGW-w64 distros for 32 & 64 bit.  
  
Ok, but the 32-bit build fails as if its Windows SDK headers are incomplete, which makes me suspect it's MinGW32 rather than a 32-bit target of MinGW-w64.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2024-08-25 16:59:46 UTC  
> Url: https://github.com/boostorg/filesystem/pull/322#issuecomment-2308925305  

> I see no point in running a C++03 CI build when it is not expected to work (whether with an error or silently skip the build is not important).  
  
The intention was to verify the skipping works, i.e. the linked code does what it is supposed to. But I removed the jobs.  
  
> Ok, but the 32-bit build fails as if its Windows SDK headers are incomplete, which makes me suspect it's MinGW32 rather than a 32-bit target of MinGW-w64.  
  
The 32-bit MinGW-w64 does not define `__MINGW64__` hence the check was to restrictive. Googling around I don't see a comparable check used anywhere but I did find a comment in CMake that I copied here: https://gitlab.kitware.com/cmake/cmake/-/blob/master/Source/kwsys/SystemTools.cxx#L121-122  
  
I.e. the struct is only accessible to drivers and there wasn't a configuration tested yet that relied on it to be defined by a Windows SDK header

---

## Comment 5

> Username: Flamefire  
> Created_at: 2024-08-26 10:36:31 UTC  
> Url: https://github.com/boostorg/filesystem/pull/322#issuecomment-2309894024  

CI is passing now in all configs after unconditionally declaring the struct only available to drivers. Anything else?

---

## Comment 6

> Username: Lastique  
> Created_at: 2024-08-26 11:13:11 UTC  
> Url: https://github.com/boostorg/filesystem/pull/322#issuecomment-2309954818  

Thank you.

---
