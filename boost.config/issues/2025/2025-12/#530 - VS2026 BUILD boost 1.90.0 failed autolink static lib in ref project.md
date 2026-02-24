# #530 - VS2026 BUILD boost 1.90.0 failed autolink static lib in ref project [Closed]

> Username: lingerer  
> Created at: 2025-12-11 07:47:39 UTC  
> Updated at: 2026-01-22 09:00:11 UTC  
> Closed at: 2026-01-22 09:00:11 UTC  
> Url: https://github.com/boostorg/config/issues/530  

When I use cmake+vs2026 build boost 1.90.0, I got static lib like:  
libboost_chrono-vc145-mt-x64-1_90.lib  
But If it link with related header,auto link direct to:  
libboost_chrono-vc143-mt-x64-1_90.lib  
  
I check this with BOOST_LIB_DIAGNOSTIC define and they all print vc143,then I check the source in auto_link.hpp:  
  
> #  elif defined(BOOST_MSVC) && (BOOST_MSVC < 1930)  
>   
>      // vc14.2:  
> #    define BOOST_LIB_TOOLSET "vc142"  
>   
> #  elif defined(BOOST_MSVC)  
>   
>      // vc14.3:  
> #    define BOOST_LIB_TOOLSET "vc143"  
  
 BOOST_MSVC IS _MSC_VER from msvc compiler which it was 1944 in vs2022 but 1950 in vs2026  
  
So the best solution now is  add similar define like   
  
> #  elif defined(BOOST_MSVC) && (BOOST_MSVC < 1950)  
>   
>      // vc14.3:  
> #    define BOOST_LIB_TOOLSET "vc143"  
> #  elif defined(BOOST_MSVC)  
>   
>      // vc14.5:  
> #    define BOOST_LIB_TOOLSET "vc145"`

---

## Comment 1

> Username: SSimboeckMesu  
> Created at: 2026-01-20 09:42:38 UTC  
> Url: https://github.com/boostorg/config/issues/530#issuecomment-3771936171  

[boost_install](https://github.com/boostorg/boost_install)/BoostDetectToolset.cmake  
  
It seems that Visual Studio 2026 is not handled in this file.  
  
line 99:  
```cmake  
  if((MSVC_VERSION GREATER 1929) AND (MSVC_VERSION LESS 1950))  
  
    set(BOOST_DETECTED_TOOLSET "vc143")  
  
  elseif((MSVC_VERSION GREATER 1919) AND (MSVC_VERSION LESS 1930))  
```  
  
You would need something like:  
```cmake  
  if((MSVC_VERSION GREATER 1949) AND (MSVC_VERSION LESS ????))  
  
    set(BOOST_DETECTED_TOOLSET "vc145")  
  
  elseif((MSVC_VERSION GREATER 1929) AND (MSVC_VERSION LESS 1950))  
  
    set(BOOST_DETECTED_TOOLSET "vc143")  
```

---

## Comment 2

> Username: pdimov  
> Created at: 2026-01-22 09:00:11 UTC  
> Url: https://github.com/boostorg/config/issues/530#issuecomment-3783283489  

This should have been fixed by https://github.com/boostorg/config/commit/56239bfd885defdafb673680c8e763380766f948.
