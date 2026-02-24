# #7 - Boost_LIBRARIES not defined [Closed]

> Username: AdamMajer  
> Created at: 2019-05-09 10:46:30 UTC  
> Updated at: 2020-05-06 19:30:54 UTC  
> Closed at: 2020-05-06 19:30:54 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7  

```  
 # It's roughly, but not perfectly, compatible with the behavior  
 # of find_package(Boost) as provided by FindBoost.cmake.  
```  
  
Having read that, in practice, it's not really compatible with the current software out there. This makes it not very useful for distributions like openSUSE Tumbleweed or related for now. The main problem is that `Boost_LIBRARIES` is not set. So even after finding all the libraries, software just doesn't link against Boost.  
  
And since this package is basically overriding the cmake's version of FindBoost, the only solution is not to ship Boost's version for now. For example,   
  
https://build.opensuse.org/package/show/home:adamm:boost_test/ledger  
  
fails with link errors because Boost_LIBRARIES is undefined. This package basically just uses these setting,  
  
```  
find_package(Boost 1.49.0  
  REQUIRED date_time filesystem system iostreams regex unit_test_framework  
  ${BOOST_PYTHON})  
  
include_directories(SYSTEM ${Boost_INCLUDE_DIRS})  
link_directories(${Boost_LIBRARY_DIRS})  
  
set(CMAKE_REQUIRED_INCLUDES ${CMAKE_INCLUDE_PATH} ${Boost_INCLUDE_DIRS})  
set(CMAKE_REQUIRED_LIBRARIES ${Boost_LIBRARIES} icuuc ${PROFILE_LIBS})  
  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2019-05-09 15:28:01 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-490951707  

In an ideal world, projects will be using "modern CMake" and imported targets, but it appears that we aren't in it.  
  
I suppose I can, after `find_package(Boost COMPONENTS x y z)`, set `Boost_LIBRARIES`  to `Boost::x Boost::y Boost::z`, which should work for most existing code.

---

## Comment 2

> Username: AdamMajer  
> Created at: 2019-05-09 15:42:47 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-490957521  

On 5/9/19 5:28 PM, Peter Dimov wrote:  
> In an ideal world, projects will be using "modern CMake" and imported  
> targets, but it appears that we aren't in it.  
>   
> I suppose I can, after |find_package(Boost COMPONENTS x y z)|, set  
> |Boost_LIBRARIES| to |Boost::x Boost::y Boost::z|, which should work for  
> most existing code.  
  
In an ideal world :) but in real world, we have to fix nghttp2 now  
because it's still using deprecated functionality from Boost.Asio that  
was now removed almost half a decade since deprecation.  
  
Thank you for addressing this. I'll test it here when you have this  
working. Fixing a few packages is OK, but this affected 60+  
  
- Adam

---

## Comment 3

> Username: pdimov  
> Created at: 2019-05-09 21:40:03 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-491078703  

This: https://github.com/boostorg/boost_install/commit/cfa8d55250dfc2635e907e42da423e4eb540dee5 should take care of the easy cases.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-05-18 19:37:34 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-493701850  

Adam, please let me know if you've tested these changes, and if there's anything else we need to do to avoid too many packages breaking.

---

## Comment 5

> Username: AdamMajer  
> Created at: 2019-05-27 09:08:19 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-496140399  

I've tested this patch and it does improve things quite a bit. Most of the cmake based packages now build. But I still have to fix my packaging and I'll let you know if there is anything else that can be addressed here.

---

## Comment 6

> Username: pdimov  
> Created at: 2019-05-27 13:09:43 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-496207628  

Thanks. Note that I added some more compatibility variables in https://github.com/boostorg/boost_install/commit/c010d4739270a4fbeecc03af1410e48dcc45e20f, although they may not make much difference in practice.

---

## Comment 7

> Username: ekpyron  
> Created at: 2019-06-07 10:43:28 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-499840246  

> In an ideal world, projects will be using "modern CMake" and imported targets, but it appears that we aren't in it.  
  
One of the problems here is that cmake's ``FindBoost`` module only defines imported targets if the cmake version is *newer* than the respective boost version and that's often not the case... (at least that's the reason why we didn't switch to imported targets sooner).

---

## Comment 8

> Username: ekpyron  
> Created at: 2019-06-07 11:14:55 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-499847669  

@pdimov In https://github.com/boostorg/boost_install/commit/cfa8d55250dfc2635e907e42da423e4eb540dee5 you have the line ``set(Boost_LIBRARIES)``. Why is that? Won't this *reset* the list of libraries and it'll end up being empty? (see https://cmake.org/cmake/help/latest/command/set.html: `` Zero arguments will cause normal variables to be unset.``)

---

## Comment 9

> Username: Flamefire  
> Created at: 2019-06-07 11:18:46 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-499848549  

@ekpyron You are partially right. The variable will be set by `boost_find_dependency` later, but the `set` used makes it use the CACHE variable (if any)

---

## Comment 10

> Username: ekpyron  
> Created at: 2019-06-07 11:29:41 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-499851015  

@Flamefire Ah, right, I didn't pay attention to the fact that the ``list(APPEND``s are in a function that's only called later. Thanks!

---

## Comment 11

> Username: Flamefire  
> Created at: 2019-06-13 06:36:46 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-501569401  

FYI: I'm working with the CMake guys to get these legacy variables into FindBoost to be able to get them out BoostConfig in the future. BoostConfig shouldn't have to set anything but the version and the targets but currently needs to due to backwards compatibility for users relying on FindBoost.  
  
See discussions in https://gitlab.kitware.com/cmake/cmake/merge_requests/3438

---

## Comment 12

> Username: ekpyron  
> Created at: 2019-06-13 08:11:35 UTC  
> Updated at: 2019-06-13 08:31:18 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-501598471  

@Flamefire I don't think that will work, though - ``find_package(Boost)`` will use BoostConfig, if it finds it, under any circumstances (This is actually a mistake in the ``FindBoost`` module that falls back to Config mode, of course, but this mistake will be shipped everywhere for a long time). That's why installing BoostConfig in 1.70 actively breaks existing build setups...  
  
So *if* you remove the legacy variables from BoostConfig, you will force everybody who wants to support multiple boost versions to employ workarounds on their end (especially since ``FindBoost`` *might*, but also *might not* define imported targets depending on whether cmake is newer than boost or not - which in itself is an issue that should be fixed on their end as far as possible).  
  
It's a bad situation - especially since people will need workarounds already, since 1.70 itself *will* break regular builds (which are very likely not to use imported target, but the legacy variables due to ``FindBoost`` not reliably defining imported targets). I don't think there's a really clean short-term solution that won't cause problems and I think the best solution is to keep the legacy variables in BoostConfig.

---

## Comment 13

> Username: ekpyron  
> Created at: 2019-06-13 08:22:12 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-501602242  

I'd actually even suggest to you to quickly release 1.70.1 including those compatibility variables in BoostConfig - otherwise you will cause hell for every package maintainer wanting to ship 1.70 ;-). Or 1.70 just won't get packaged anywhere any time soon :-).

---

## Comment 14

> Username: ekpyron  
> Created at: 2019-06-13 08:40:42 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-501608484  

Of course cmake should fix it on their end as well, though, so :+1: for that.

---

## Comment 15

> Username: Flamefire  
> Created at: 2019-06-13 08:44:20 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-501609740  

> I'd actually even suggest to you to quickly release 1.70.1 including those compatibility variables in BoostConfig - otherwise you will cause hell for every package maintainer wanting to ship 1.70 ;-).  
  
Totally agreed!  
  
> especially since FindBoost might, but also might not define imported targets depending on whether cmake is newer than boost or not - which in itself is an issue that should be fixed on their end as far as possible  
  
This is something users (and package maintainers) need to take care off and shouldn't be an issue as it has been like this for a while now to be known. It is simply a new dependency: New Boost -> New CMake. But yes this is kinda annoying but there is no real fix for that anyway as future dependencies are not known. Using the BoostConfig is the best option there as it will have the targets for **any** new boost version without any CMake update.  
  
> So if you remove the legacy variables from BoostConfig, you will force everybody who wants to support multiple boost versions to employ workarounds on their end  
  
See the discussion in the linked PR. The goal is not to remove them **now** but sometime in the future. I mean: Those legacy variables are only required for projects using them instead of the targets. So projects should already move to using those targets. Also does were originally only defined by FindBoost so that is where they should be defined for compatibility for users relying on FindBoost(-like) behavior (note that you could use a BoostConfig directly with `NO_MODULE`)     
Of course the problem is that current FindBoost users may still rely on those variables but hopefully they will lessen.   
  
In the future Boost will be able to drop those variables from BoostConfig and projects still using them can require CMake 3.15+ which will define them for compatibility.

---

## Comment 16

> Username: ekpyron  
> Created at: 2019-06-13 09:08:26 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-501618454  

Alright, great! And yes, in general having BoostConfig shipped by boost itself is *very* great, because it will finally allow people to actually move to imported targets in the future (since so far I think in practice the "new boost" depends on "new cmake" situation for imported targets effectively prevents people from using them).

---

## Comment 17

> Username: AdamMajer  
> Created at: 2019-06-13 09:51:02 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-501633612  

On 6/13/19 10:22 AM, Daniel Kirchner wrote:  
> I'd actually even suggest to you to quickly release 1.70.1 including  
> those compatibility variables in BoostConfig - otherwise you will cause  
> hell for every package maintainer wanting to ship 1.70 ;-). Or 1.70 just  
> won't get packaged anywhere any time soon :-).  
  
Distributions can patch their Boost versions, so this is not a ship  
stopper. For openSUSE there is a bigger problem with python MPI  
extension. More of a problem with Boost.Build than the cmake :)  
  
- Adam

---

## Comment 18

> Username: himikof  
> Created at: 2019-10-10 14:43:54 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-540621224  

Unfortunately, cfa8d55 breaks backward compatibility in some cases:  
  
For example, https://github.com/facebook/folly uses the following pattern:  
```  
find_package(Boost 1.51.0 MODULE  
  COMPONENTS  
    context  
    filesystem  
    program_options  
    regex  
    system  
    thread  
  REQUIRED  
)  
add_library(folly_deps INTERFACE)  
target_link_libraries(folly_deps INTERFACE ${Boost_LIBRARIES})  
target_link_libraries(folly PUBLIC folly_deps)  
install(TARGETS folly folly_deps ...  
  EXPORT folly  
)  
install(  
  EXPORT folly  
  DESTINATION ${CMAKE_INSTALL_DIR}  
  NAMESPACE Folly::  
  FILE folly-targets.cmake  
  COMPONENT dev  
)  
```  
This pattern essentially writes contents of the `Boost_LIBRARIES` variable to the `folly-targets.cmake` file. This pattern seems OK, and always worked properly, because until cfa8d55 `Boost_LIBRARIES` only ever contained absolute file paths to the libraries, not target names.  
Target names could only be used in CMake exported config files when they are explicitly defined there, or guaranteed to exist in all consumers.  
  
So, after upgrading to new Boost and CMake, CMake-based consumers of folly library start failing, while the library itself builds fine (just generates invalid export files).  
  
See CMake issue https://gitlab.kitware.com/cmake/cmake/issues/19816 for CMake side of the problem.   
  
Unfortunately, it is not clear to me whether it is a CMake FindBoost issue (which added it's own emulation of legacy variables in 3.15), or boost cmake configs issue, or both. The table in the CMake issue shows that the behavior is inconsistent and version-dependent.

---

## Comment 19

> Username: Flamefire  
> Created at: 2019-10-10 14:57:56 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-540627550  

> The table in the CMake issue shows that the behavior is inconsistent and version-dependent.  
  
Partially: Boost <=1.69 had no Config, hence FindBoost did define absolute paths. Boost 1.70 has a config, but das not define `Boost_LIBRARIES` so FindBoost does it, and Boost 1.71 does define the variable. So only difference: Boost <=1.69 defines the variables to absolute paths, with Boost 1.70+ you get target names.  
  
Usually (as per the CMake docu) you'd have a couple of `find_dependency` calls in the config file which is installed. I'd argue that dependent projects need to be updated to do that in order to be compatible with newer Boost versions. Changing the contents of the variable now will introduce another change in what the variable contains.  
  
However I don't mind a change here. Just consider that it might be harder to implement as you need to get the libraries out of the targets and handle alias targets.

---

## Comment 20

> Username: bradking  
> Created at: 2019-10-10 15:07:12 UTC  
> Url: https://github.com/boostorg/boost_install/issues/7#issuecomment-540631783  

@himikof the consumer-of-consumer case you're describing is common with target-based CMake packages.  Folly's package configuration file (`<PackageName>Config.cmake`) needs to `find_dependency(Boost)` or use some equivalent way to make its dependencies available to its consumers.  Using just absolute paths can leave out usage requirements.  Folly's dependents (consumers) need to use `BoostConfig.cmake`'s targets transitively through Folly in order to hook up usage requirements.
