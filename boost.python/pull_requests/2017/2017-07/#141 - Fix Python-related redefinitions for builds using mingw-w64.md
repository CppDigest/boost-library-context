# #141 Fix Python-related redefinitions for builds using mingw-w64 [Open]

> Username: sav-ix  
> Created at: 2017-07-20 06:23:20 UTC  
> Updated at: 2017-12-19 08:02:51 UTC  
> Url: https://github.com/boostorg/python/pull/141  

Fixes https://github.com/boostorg/python/issues/140.  
Tested for builds using mingw-w64 with `<Debug|Release><Shared|Static>` configurations.  
UPDATE: But it broke MSVC builds. Any suggestions?

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2017-07-20 12:13:21 UTC  
> Url: https://github.com/boostorg/python/pull/141#issuecomment-316684656  

By "broke MSVC builds", are you referring to the Appveyor build failure ? That's an unrelated issue with the SCons build logic. Boost.Build is not involved in the CI builds. If not, can you show an error log ? (I'm only a casual Windows user, so I may not be able to help.)

---

## Comment 2

> Username: sav-ix  
> Created_at: 2017-07-20 12:37:10 UTC  
> Url: https://github.com/boostorg/python/pull/141#issuecomment-316689695  

Travis failures forced me to test this patch for MSVC builds. Which I missed to do before publishing this PR.  
For builds using MSVC with Debug+Static and Release+Shared configurations got the same errors:  
```  
error: "gcc" is not a known value of feature <toolset>  
error: legal values: "msvc"  
```  
(see [logs](https://github.com/boostorg/python/files/1162258/boost_msvc_build_logs.zip) in attachment).

---

## Review 3 [Commented]

> Username: stefanseefeld  
> Created_at: 2017-07-20 14:20:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/141#pullrequestreview-51216685  

📁 build/Jamfile

```diff
  38 |   : source-location ../src
  39 |+   : requirements <toolset>gcc <target-os>windows:<cxxflags>-D_hypot=hypot
  40 |+   : requirements <toolset>gcc <target-os>windows <link>shared:<cxxflags>-DMS_WIN64
```

> Username: stefanseefeld  
> Created_at: 2017-07-20 14:20:19 UTC  
> Url: https://github.com/boostorg/python/pull/141#discussion_r128528388  

I don't think this is doing what you expect. As far as I understand Boost.Build syntax, the last line above defines as requireents:  
* toolset=gcc  
* target-os=windows  
* cxxflags=-DMS_WIN64, whenever link=shared  
  
It looks like what you intend to do is to define -DMS_WIN64 whenever all three features (toolset, target-os, and link) have the given values, yes ?   
Please ask on the boost or boost.build mailing list what the right syntax for this is.  
(I'm also not sure how this would fix the `"gcc is not a known value..." error. That's another thing to ask on boost.build ML).  
Sorry I can't help more with this. Boost.Build is complicated and badly documented, which is one reason why I'm trying to move away from it...


---

## Comment 4

> Username: sav-ix  
> Created_at: 2017-07-20 14:57:15 UTC  
> Url: https://github.com/boostorg/python/pull/141#issuecomment-316729739  

I was trying to add rules to Boost.Python build system:  
IF build using `mingw-w64` THEN add `-D_hypot=hypot` to compiler keys,  
IF build using `mingw-w64` AND layout `shared` THEN add `-DMS_WIN64` to compiler keys,  
based on other Jamfiles as samples. But Boost.Build was more complicated then expected.  
  
> Please ask on the boost or boost.build mailing list what the right syntax for this is.  
  
Or we can invite Boost.Build Developers to join this discussion.  
@raffienficiaud, could you give a tip, how to solve this issue correctly?

---
