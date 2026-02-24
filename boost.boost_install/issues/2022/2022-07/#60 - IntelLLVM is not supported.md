# #60 - IntelLLVM is not supported [Closed]

> Username: mablanchard  
> Created at: 2022-07-11 09:38:34 UTC  
> Updated at: 2022-11-17 17:00:30 UTC  
> Closed at: 2022-11-17 16:59:20 UTC  
> Url: https://github.com/boostorg/boost_install/issues/60  

Intel C/C++ compilers [switched to LLVM](https://www.intel.com/content/www/us/en/developer/articles/technical/adoption-of-llvm-complete-icx.html) and new ICX compiler is reported with `CMAKE_CXX_COMPILER_ID` set to `IntelLLVM` by CMake (testing CMake 3.23.1). This new string seems not to be handled correctly by [`BoostDetectToolset.cmake`](https://github.com/boostorg/boost_install/blob/develop/BoostDetectToolset) and throws messages like:  
  
> -- Boost toolset is unknown (compiler IntelLLVM 2022.1.0)

---

## Comment 1

> Username: pdimov  
> Created at: 2022-07-11 10:52:20 UTC  
> Url: https://github.com/boostorg/boost_install/issues/60#issuecomment-1180247799  

@grafikrobot @grisumbras What is the correct b2 toolset name for the new LLVM-based Intel compiler?

---

## Comment 2

> Username: grafikrobot  
> Created at: 2022-07-11 13:06:09 UTC  
> Url: https://github.com/boostorg/boost_install/issues/60#issuecomment-1180386756  

@pdimov it's just `intel` as before. Not sure which of the four platform variants of it support the new compiler though.

---

## Comment 3

> Username: mablanchard  
> Created at: 2022-09-20 16:06:02 UTC  
> Url: https://github.com/boostorg/boost_install/issues/60#issuecomment-1252576851  

For reference, the hack below _seems_ to be working here:  
  
```  
--- lib/cmake/BoostDetectToolset-1.77.0.cmake	(revision 2786)  
+++ lib/cmake/BoostDetectToolset-1.77.0.cmake	(working copy)  
@@ -32,7 +32,7 @@  
     set(_BOOST_COMPILER_VERSION_MINOR)  
   endif()  
   
-elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Intel")  
+elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Intel" OR CMAKE_CXX_COMPILER_ID STREQUAL "IntelLLVM")  
   
   if(WIN32)  
```

---

## Comment 4

> Username: pdimov  
> Created at: 2022-11-16 18:19:01 UTC  
> Url: https://github.com/boostorg/boost_install/issues/60#issuecomment-1317460257  

Applied your suggestion on develop, https://github.com/boostorg/boost_install/commit/56f7c5da02a62c009954bfa0c9ccb1b57a3096a4. Sorry that didn't happen earlier; the notification must have come in an inopportune time so I've missed it.  
  
It's a bit too late now for 1.81, the beta is already on its way, but I'll see if this can be applied post-beta.

---

## Comment 5

> Username: mablanchard  
> Created at: 2022-11-17 08:27:50 UTC  
> Url: https://github.com/boostorg/boost_install/issues/60#issuecomment-1318265303  

My bad, I should have created a PR but felt like this was more of a hack rather than a proper fix. I never took the time to actually look at the CMake logic here...

---

## Comment 6

> Username: pdimov  
> Created at: 2022-11-17 16:29:01 UTC  
> Url: https://github.com/boostorg/boost_install/issues/60#issuecomment-1318892936  

Should be the proper fix.

---

## Comment 7

> Username: pdimov  
> Created at: 2022-11-17 16:59:20 UTC  
> Url: https://github.com/boostorg/boost_install/issues/60#issuecomment-1318932368  

Merged to master, should be in the final 1.81 release.

---

## Comment 8

> Username: mablanchard  
> Created at: 2022-11-17 17:00:30 UTC  
> Url: https://github.com/boostorg/boost_install/issues/60#issuecomment-1318933804  

Thanks!
