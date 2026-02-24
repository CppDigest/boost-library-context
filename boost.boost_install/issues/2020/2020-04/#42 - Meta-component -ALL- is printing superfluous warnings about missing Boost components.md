# #42 - Meta-component "ALL" is printing superfluous warnings about missing Boost components [Closed]

> Username: DenizThatMenace  
> Created at: 2020-04-22 20:42:11 UTC  
> Updated at: 2020-05-06 19:20:46 UTC  
> Closed at: 2020-05-06 19:20:46 UTC  
> Url: https://github.com/boostorg/boost_install/issues/42  

I just tested the current Boost 1.73.0 beta1 version and came across the following inconvenience:  
  
When using the new meta-component `ALL` (as in `find_package( Boost 1.73.0 REQUIRED ALL CONFIG )`) I get warnings about some Boost components for which the package configuration file is available and can be found but which says that the associated Boost component/library is not available (which is correct).  
  
I was building Boost on Linux and the following Boost components are not available although their package-configuration files are created (and installed) when building:  
  
* boost_fiber_numa  
* boost_graph_parallel  
* boost_numpy  
* boost_stacktrace_windbg  
* boost_stacktrace_windbg  
  
I do not know if the associated package-configuration files (e.g. `boost_fiber_numa-config.cmake`) should not have been installed in the first place or if the `find_package` call should have been silent about them missing.  
  
Whether these package-configuration files should be generated and installed or not, I realized that the internal `find_package` can only be silenced if the entire `find_package( Boost ... ALL ... )` call has the `QUIET` option.  
  
Is that how it is supposed to be?  
  
It should not be too hard to patch `BoostConfig.cmake` to always search quietly for individual components when using the meta-component `ALL`. (I could provide a pull-request for it if desired.)

---

## Comment 1

> Username: DenizThatMenace  
> Created at: 2020-04-22 20:53:10 UTC  
> Url: https://github.com/boostorg/boost_install/issues/42#issuecomment-618034537  

Such a superfluous warning looks like this:  
  
```  
CMake Warning at /opt/boost/lib/x86_64-linux-gnu/cmake/Boost-1.73.0/BoostConfig.cmake:141 (find_package):  
  Found package configuration file:  
  
    /opt/boost/lib/x86_64-linux-gnu/cmake/boost_fiber_numa-1.73.0/boost_fiber_numa-config.cmake  
  
  but it set boost_fiber_numa_FOUND to FALSE so package "boost_fiber_numa" is  
  considered to be NOT FOUND.  Reason given by package:  
  
  No suitable build variant has been found.  
  
Call Stack (most recent call first):  
  /opt/boost/lib/x86_64-linux-gnu/cmake/Boost-1.73.0/BoostConfig.cmake:196 (boost_find_component)  
  /opt/boost/lib/x86_64-linux-gnu/cmake/Boost-1.73.0/BoostConfig.cmake:252 (boost_find_all_components)  
  CMakeLists.txt:58 (find_package)  
```

---

## Comment 2

> Username: pdimov  
> Created at: 2020-04-22 22:29:26 UTC  
> Url: https://github.com/boostorg/boost_install/issues/42#issuecomment-618073486  

I see no such warnings in the Travis CI build.  
  
https://travis-ci.org/github/boostorg/boost_install/jobs/675426098  
  
https://github.com/boostorg/boost_install/blob/88b89e81651e3a92686717ad97b7020cbf316b5a/test/filesystem/CMakeLists.txt#L23

---

## Comment 3

> Username: DenizThatMenace  
> Created at: 2020-04-23 00:34:10 UTC  
> Url: https://github.com/boostorg/boost_install/issues/42#issuecomment-618109389  

> I see no such warnings in the Travis CI build.  
>   
> https://travis-ci.org/github/boostorg/boost_install/jobs/675426098  
>   
> https://github.com/boostorg/boost_install/blob/88b89e81651e3a92686717ad97b7020cbf316b5a/test/filesystem/CMakeLists.txt#L23  
  
I assume the reason is that all those `find_package` calls use the `QUIET` option.  
  
In fact, the Travis CI log states that it uses the QUIET option explicitly:  
```  
...  
17797  -- Found Boost 1.73.0 at /home/travis/.local/lib/cmake/Boost-1.73.0  
17798  --   Requested configuration: QUIET REQUIRED COMPONENTS ALL  
17799  -- BoostConfig: find_package(boost_headers 1.73.0 EXACT CONFIG REQUIRED QUIET HINTS /home/travis/.local/lib/cmake)  
...  
17880  -- BoostConfig: find_package(boost_fiber_numa 1.73.0 EXACT CONFIG  QUIET HINTS /home/travis/.local/lib/cmake)  
17881  -- Found boost_fiber_numa 1.73.0 at /home/travis/.local/lib/cmake/boost_fiber_numa-1.73.0  
17882  -- Boost toolset is gcc5 (GNU 5.4.0)  
17883  -- Scanning /home/travis/.local/lib/cmake/boost_fiber_numa-1.73.0/libboost_fiber_numa-variant*.cmake  
17884  --   Library has no variants and is considered not found  
...  
```  
And I am pretty sure that option comes from `FindBoost.cmake`, because that is the only way I could reproduce this.  
  
Try to add the `CONFIG` option to the `find_package` call you mentioned above (to force skipping `FindBoost.cmake`) and Travis CI should be able to reproduce the superfluous warnings I am addressing with this issue.

---

## Comment 4

> Username: pdimov  
> Created at: 2020-04-23 14:06:14 UTC  
> Url: https://github.com/boostorg/boost_install/issues/42#issuecomment-618414206  

You're correct, FindBoost uses QUIET.

---

## Comment 5

> Username: pdimov  
> Created at: 2020-04-24 17:05:15 UTC  
> Url: https://github.com/boostorg/boost_install/issues/42#issuecomment-619134642  

Should be fixed on develop.

---

## Comment 6

> Username: DenizThatMenace  
> Created at: 2020-04-24 22:54:02 UTC  
> Url: https://github.com/boostorg/boost_install/issues/42#issuecomment-619271310  

Without having it tested, your changes in https://github.com/boostorg/boost_install/commit/601755f2e81cff468caba515a8c1bbb9d0b00b8c (and https://github.com/boostorg/boost_install/commit/dc769f59a29aa87ed0580188147e2d08ed0aac48) look good to me.
