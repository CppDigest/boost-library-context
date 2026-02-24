# #26 - BoostDetectToolset.cmake doesn't match version number generated with b2 [Closed]

> Username: cdglove  
> Created at: 2019-12-31 17:02:03 UTC  
> Updated at: 2019-12-31 19:47:33 UTC  
> Closed at: 2019-12-31 19:47:33 UTC  
> Url: https://github.com/boostorg/boost_install/issues/26  

When I build my libraries on MingW using GCC 9.2, I get library names as follows:  
  
`libboost_serialization-mgw92-mt-x64-1_73.a`  
  
But, when I try to consume Boost from cmake using something like:  
  
`find_package( Boost CONFIG REQUIRED serialization)`  
  
I get an error like:  
  
```  
Found package configuration file:  
[cmake]   
[cmake]     C:/Users/cdglo/src/cmn/boost/stage/lib/cmake/boost_serialization-1.73.0/boost_serialization-config.cmake  
[cmake]   
[cmake]   but it set boost_serialization_FOUND to FALSE so package  
[cmake]   "boost_serialization" is considered to be NOT FOUND.  Reason given by  
[cmake]   package:  
[cmake]   
[cmake]   No suitable build variant has been found.  
[cmake]   
[cmake]   The following variants have been tried and rejected:  
[cmake]   
[cmake]   
[cmake]   * libboost_serialization-mgw92-mt-d-x64-1_73.a (mgw92, detected mgw9, set  
[cmake]   Boost_COMPILER to override)  
[cmake]   
[cmake]   * libboost_serialization-mgw92-mt-x64-1_73.a (mgw92, detected mgw9, set  
[cmake]   Boost_COMPILER to override)  
[cmake]   
[cmake]   * libboost_serialization-vc142-mt-gd-x64-1_73.lib (vc142, detected mgw9,  
[cmake]   set Boost_COMPILER to override)  
[cmake]   
[cmake]   * libboost_serialization-vc142-mt-x64-1_73.lib (vc142, detected mgw9, set  
[cmake]   Boost_COMPILER to override)  
[cmake]   
[cmake] Call Stack (most recent call first):  
[cmake]   C:/Users/cdglo/src/cmn/boost/stage/lib/cmake/Boost/BoostConfig.cmake:185 (boost_find_component)  
[cmake]   CMakeLists.txt:23 (find_package)  
[cmake]   
```  
  
The error being here:  
  
`mgw92, detected mgw9`  
  
I found that removing this code from BoostDetectToolset fixes this issue for me, but I'm not sure sure why that code is there.  
  
```  
elseif(MINGW)  
  
  set(BOOST_DETECTED_TOOLSET "mgw")  
  
  # if(_BOOST_COMPILER_VERSION_MAJOR GREATER 4)  
  #  set(_BOOST_COMPILER_VERSION_MINOR)**  
  # endif()  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2019-12-31 17:25:29 UTC  
> Url: https://github.com/boostorg/boost_install/issues/26#issuecomment-569962499  

BoostDetectedToolset is actually right here, mgw9 is the correct tag; b2 is wrong. I'll prepare a PR against Boost.Build.

---

## Comment 2

> Username: cdglove  
> Created at: 2019-12-31 19:47:33 UTC  
> Url: https://github.com/boostorg/boost_install/issues/26#issuecomment-569981193  

That makes sense. I can rename the libraries in the mean time.
