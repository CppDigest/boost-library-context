# #80 - VS2026 Support in BoostDetectToolset.cmake [Closed]

> Username: SSimboeckMesu  
> Created at: 2026-01-22 08:50:14 UTC  
> Updated at: 2026-01-22 09:49:59 UTC  
> Closed at: 2026-01-22 09:49:59 UTC  
> Url: https://github.com/boostorg/boost_install/issues/80  

Visual Studio 2026 is not handled in BoostDetectToolset.cmake.  
  
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
  
But what should be the maximum?  
If this is clarified, I could make the pull request.  
  
  
See also: https://github.com/boostorg/config/issues/530

---

## Comment 1

> Username: pdimov  
> Created at: 2026-01-22 08:57:47 UTC  
> Url: https://github.com/boostorg/boost_install/issues/80#issuecomment-3783273424  

The maximum should be assumed to be 1960 for now; if Microsoft exceed it (as they did for 14.3), we'll bump it then.
