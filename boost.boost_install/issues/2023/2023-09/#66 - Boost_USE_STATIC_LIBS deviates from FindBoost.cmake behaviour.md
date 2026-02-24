# #66 - Boost_USE_STATIC_LIBS deviates from FindBoost.cmake behaviour [Open]

> Username: jpfeuffer  
> Created at: 2023-09-01 07:25:47 UTC  
> Updated at: 2025-11-16 11:05:57 UTC  
> Url: https://github.com/boostorg/boost_install/issues/66  

In the Find module, Boost_USE_STATIC_LIBS=OFF means that it is allowed to find either a dynamic version of boost **or** a static version.  
  
With your config file, Boost_USE_STATIC_LIBS=OFF forces shared-only.  
  
a) Why the deviation? This makes consistent CMake scripts very difficult if one wants to support older boosts without the config.  
~~b) Why those arbitrary choices for the value OFF? Shouldn't the consumer decide?! How would one even choose shared/dynamic boost on Windows with this?~~ Edit: corrected  
  
While this is considered, can you recommend a quick fix on how to use this config to find either shared or static? After all, many builds are agnostic to how boost was linked.

---

## Comment 1

> Username: jpfeuffer  
> Created at: 2023-09-01 07:32:55 UTC  
> Updated at: 2023-09-01 07:33:12 UTC  
> Url: https://github.com/boostorg/boost_install/issues/66#issuecomment-1702303764  

Sorry, I just saw that I confused OFF with not set, or overlooked the "third option" of not setting the variable. Let me reevaluate.

---

## Comment 2

> Username: jpfeuffer  
> Created at: 2023-09-01 07:39:09 UTC  
> Url: https://github.com/boostorg/boost_install/issues/66#issuecomment-1702311419  

Ok so ignore point b) but the rest of the issue still stands:  
  
- Why deviate from the FindModule for the *same* variable?  
- How to look for both, either shared or static? (Without triggering two searches where one will unnecessarily clutter the CMake configuration logs.)

---

## Comment 3

> Username: jpfeuffer  
> Created at: 2023-09-01 07:43:47 UTC  
> Updated at: 2023-09-01 07:44:26 UTC  
> Url: https://github.com/boostorg/boost_install/issues/66#issuecomment-1702316499  

IMHO the behaviour should be at least:  
  
(changes in bold)  
> Boost_USE_STATIC_LIBS:    When ON, uses static Boost libraries; when OFF,  
>                           uses shared Boost libraries; when not set, **PREFERS**  
>                           static on Windows, shared otherwise **but accepts both**.  
  
Or just follow the behaviour from the FindModule.

---

## Comment 4

> Username: pdimov  
> Created at: 2023-09-01 16:42:12 UTC  
> Url: https://github.com/boostorg/boost_install/issues/66#issuecomment-1703029069  

Our generated config files don't have the ability to accept both static and shared. It needs to be chosen beforehand. That's why we don't match the FindBoost behavior.

---

## Comment 5

> Username: jpfeuffer  
> Created at: 2023-09-01 18:13:23 UTC  
> Url: https://github.com/boostorg/boost_install/issues/66#issuecomment-1703152366  

I see. Is this a general limitation of how boost is designed or was it "just not implemented yet"? If it's a limitation, can you very shortly elaborate on the problem?  
  
In general I think CMake config files can support that.

---

## Comment 6

> Username: Andrej730  
> Created at: 2025-11-16 11:05:57 UTC  
> Url: https://github.com/boostorg/boost_install/issues/66#issuecomment-3538536603  

Also stumbled upon this - on windows, if there are multiple variants, it's preferring static over shared - basically implying `Boost_USE_STATIC_LIBS=ON` by default.  
  
<img width="1159" height="303" alt="Image" src="https://github.com/user-attachments/assets/b3e63412-592c-4501-a41c-d1e9d07326ee" />  
  
  
Previously I thought I could rely on https://cmake.org/cmake/help/latest/module/FindBoost.html as boost cmake configs format description, but I guess it's not entirely reliable.  
  
  
In theory there should be a dedicated page on boost cmake config variables, where this can be documented (and perhaps other differences, if they exist).  
  
If anyone else stumbling on this - the conclusion is, if you plan to support both boost configs and modules (FindBoost) and want to keep it consistent, then the script should explicitly set `Boost_USE_STATIC_LIBS` as `OFF` and not rely on unset `Boost_USE_STATIC_LIBS` interpreted as `OFF` by FindBoost.
