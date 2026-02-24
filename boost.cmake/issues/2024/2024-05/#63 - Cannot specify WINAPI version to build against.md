# #63 - Cannot specify WINAPI version to build against [Open]

> Username: sodevel  
> Created at: 2024-05-28 16:03:19 UTC  
> Updated at: 2025-12-14 08:14:31 UTC  
> Url: https://github.com/boostorg/cmake/issues/63  

With `b2` i can specify the Windows API version to build against with e.g. `define=BOOST_USE_WINAPI_VERSION=0x0601` but unless i miss something this cannot be done for the CMake build.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-05-28 16:16:46 UTC  
> Url: https://github.com/boostorg/cmake/issues/63#issuecomment-2135641103  

@Lastique what do you think about this? A CMake option in `winapi` comes to mind.

---

## Comment 2

> Username: Lastique  
> Created at: 2024-05-28 16:31:25 UTC  
> Url: https://github.com/boostorg/cmake/issues/63#issuecomment-2135676379  

In my libraries, I'm mirroring C++ defines to CMake options, so one would specify e.g. `-DBOOST_USE_WINAPI_VERSION=0x0601` in cmake command line and that translates to the equivalent C++ define. I'll add this in Boost.WinAPI's CMakeLists.txt.

---

## Comment 3

> Username: Lastique  
> Created at: 2024-05-28 17:09:15 UTC  
> Url: https://github.com/boostorg/cmake/issues/63#issuecomment-2135741057  

Done in https://github.com/boostorg/winapi/commit/c5fb9c86e8b9ea460fc6d7255d15d69d4d2705ae.

---

## Comment 4

> Username: sodevel  
> Created at: 2024-05-30 16:27:49 UTC  
> Url: https://github.com/boostorg/cmake/issues/63#issuecomment-2140165278  

Not sure if this is the right place for this nor am i involved with boost and know their policies, but looking at the cache variables, it looks like they are following the form `BOOST_<LIBRARY>_FOO_BAR`. Your introduced variable does violate this form, a valid variant would look like `BOOST_WINAPI_USE_WINAPI_VERSION`.

---

## Comment 5

> Username: pdimov  
> Created at: 2024-05-30 16:38:39 UTC  
> Url: https://github.com/boostorg/cmake/issues/63#issuecomment-2140201242  

That's OK because it mirrors the macro name.

---

## Comment 6

> Username: Lastique  
> Created at: 2024-05-30 21:40:51 UTC  
> Url: https://github.com/boostorg/cmake/issues/63#issuecomment-2140905998  

The intention was that `BOOST_USE_WINAPI_VERSION` would be a Boost-wide macro that affects all libraries using Windows API.

---

## Comment 7

> Username: nigels-com  
> Created at: 2025-12-14 08:14:31 UTC  
> Url: https://github.com/boostorg/cmake/issues/63#issuecomment-3650464235  

@sodevel Anything further needed for this one?  
Can the ticket be closed?
