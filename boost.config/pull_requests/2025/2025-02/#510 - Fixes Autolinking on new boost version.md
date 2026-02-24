# #510 Fixes Autolinking on new boost version [Open]

> Username: OlekRaymond  
> Created at: 2025-02-14 10:46:30 UTC  
> Updated at: 2025-10-01 14:12:39 UTC  
> Url: https://github.com/boostorg/config/pull/510  

Fixes #496

---

## Comment 1

> Username: neusdan  
> Created_at: 2025-09-29 09:43:22 UTC  
> Url: https://github.com/boostorg/config/pull/510#issuecomment-3346001809  

I think that is not the correct fix because MSVC 19.40 is still `vc143`. I think #497 is more appropriate.

---

## Comment 2

> Username: OlekRaymond  
> Created_at: 2025-09-29 14:33:12 UTC  
> Updated_at: 2025-09-29 14:33:46 UTC  
> Url: https://github.com/boostorg/config/pull/510#issuecomment-3347331142  

> I think that is not the correct fix because MSVC 19.40 is still `vc143`. I think #497 is more appropriate.  
  
I don't think so,  
  
We had a bug where this fixed it, but it is now moot to me, so happy for this not to go in.   
  
I am on windows so happy to download the latest pre-release and find out if you'd like 😄

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2025-09-29 15:23:11 UTC  
> Url: https://github.com/boostorg/config/pull/510#issuecomment-3347628528  

I'm looking into this now, we need to do *exactly* the same thing as Boost.Build.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2025-09-29 15:44:48 UTC  
> Url: https://github.com/boostorg/config/pull/510#issuecomment-3347742087  

So far as I can tell from current develop, I still get "143" in library names, I haven't been able to spot where Boost.Build sets this though.  Attempting to build with toolset=msvc-14.4 produces:  
  
```  
error: "14.4" is not a known subfeature value of <toolset>msvc  
```  
  
Can you confirm?

---

## Comment 5

> Username: neusdan  
> Created_at: 2025-09-29 15:59:50 UTC  
> Url: https://github.com/boostorg/config/pull/510#issuecomment-3347819863  

> , I still get "143" in library names  
  
That's weird because if i set `toolset=msvc-14.4` then it will produce library names with `vc144` in it.   
  
Not sure if it is related but i am also calling `vsdevcmd.bat -vcvars_ver=14.4` before calling `b2.exe`.

---

## Comment 6

> Username: OlekRaymond  
> Created_at: 2025-09-29 16:14:44 UTC  
> Updated_at: 2025-09-29 16:25:12 UTC  
> Url: https://github.com/boostorg/config/pull/510#issuecomment-3347888436  

> I still get "143" in library names  
  
This is what happened to us.  
  
  
I have experienced oddities with setting `vcvars`, we just used the default for the latest version then downgraded to get around this issue

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2025-09-30 08:17:23 UTC  
> Url: https://github.com/boostorg/config/pull/510#issuecomment-3350590515  

>That's weird because if i set toolset=msvc-14.4 then it will produce library names with vc144 in it.  
  
I don't see how you can do that, I checked the source (https://github.com/boostorg/build/blob/develop/src/tools/msvc.jam) and there is no such thing as a "14.4" version, and for me attempting to use it, generates a hard error from b2 (which it should).  This looks like user error to me since we only support 14.3 as a valid version not 14.4.    
  
I don't know how you managed to slip by the error checks though.  
  
@grafikrobot would you have any idea what's happening at the b2 end of things?

---

## Comment 8

> Username: jdoubleu  
> Created_at: 2025-09-30 12:55:40 UTC  
> Url: https://github.com/boostorg/config/pull/510#issuecomment-3351971754  

> I don't see how you can do that, I checked the source (https://github.com/boostorg/build/blob/develop/src/tools/msvc.jam) and there is no such thing as a "14.4" version  
  
Looks like https://github.com/boostorg/build/blob/develop/src/tools/msvc.jam#L1157 does in fact allow a value of `14.4`?  
  
> and for me attempting to use it, generates a hard error from b2 (which it should).  
  
Which toolsets do you have installed on your system (I've got `14.44.35207`)? They're usually installed in `C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Auxiliary\Build\`.  
  
---  
I think the main issue is, that Microsoft broke the assumption that you can derive the 'Platform Toolset' version (e.g. `v142`, `v143`, etc.) from the actual toolset, e.g. `14.29.30133` for `v142` and `19.31.31103` for `v143` (i.e. the number after the dot `19.x` determined the least-significant digit in `v14x`).   
  
See https://devblogs.microsoft.com/cppblog/msvc-toolset-minor-version-number-14-40-in-vs-2022-v17-10/ for more information.  
  
This even caused some issues in their build systems as well.  
  
---  
  
In our case, we'd like to build with the `14.44.35207` toolset, but still have `vc143` in their names, because the toolset produces binaries that are ABI compatible across all `v143` 'Platform Toolset' versions.  
  
B2 is probably deriving the name just from the toolset version, which is now broken.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2025-10-01 14:08:39 UTC  
> Url: https://github.com/boostorg/config/pull/510#issuecomment-3356530260  

So I just updated to the latest and greatest VS-17.14, which has msvc version 194435217.  For me Boost.Build is still identifying that as msvc-14.3 and naming binaries appropriately.  However.... the update has mangled something badly, as the IDE will no longer build C++ programs (that's the second update in a row that's done that, I'll have to completely remove and reinstall to fix - when I have the time).  
  
Meanwhile I'd still like to hear from @grafikrobot what's intended in Boost.Build, as ultimately the only thing that matters is that we do the same thing.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2025-10-01 14:09:04 UTC  
> Url: https://github.com/boostorg/config/pull/510#issuecomment-3356532036  

@pdimov might have a view also.

---

## Comment 11

> Username: pdimov  
> Created_at: 2025-10-01 14:12:39 UTC  
> Url: https://github.com/boostorg/config/pull/510#issuecomment-3356548710  

Even though the toolset version is 19.4x, it's still toolset v143, which corresponds to msvc-14.3. There's no such thing as msvc-14.4. The new Visual Studio will be v145, msvc-14.5.

---
