# #1081 Boost cmake build requires CMake 3.14 or above [Closed]

> Username: nigels-com  
> Created at: 2025-09-06 00:41:54 UTC  
> Updated at: 2025-10-31 10:40:08 UTC  
> Closed at: 2025-10-31 10:40:08 UTC  
> Url: https://github.com/boostorg/boost/pull/1081  

I've been poking around with the cmake build of boost.  
See also [boostorg/nowide#199](https://github.com/boostorg/nowide/pull/199) and [boostorg/parser#277](https://github.com/boostorg/parser/issues/277)  
  
What I'm finding on Ubuntu 24.04 is that boost requires [cmake 3.19](https://cmake.org/cmake/help/latest/release/3.19.html) or newer.  
Various things break with [cmake 3.18](https://cmake.org/cmake/help/latest/release/3.18.html) in my experiments.  
  
It seems possible that work could be done in various libraries to support older versions of cmake.  
But it's not clear that work would be tidy, maintainable or valuable in a widespread sense.  
  
So the proposal here is that boost simply _requires_ cmake 3.19 and fails early and clearly otherwise.  
  
```  
$ PATH=/opt/cmake-3.18.0-Linux-x86_64/bin:$PATH cmake .. -G Ninja -DCMAKE_BUILD_TYPE=Release -DBUILD_TESTING=N && ninjaCMake Error at CMakeLists.txt:5 (cmake_minimum_required):  
  CMake 3.19 or higher is required.  You are running version 3.18.0  
  
  
-- Configuring incomplete, errors occurred!  
```  
  
I think this change would also be helpful in terms of library maintainers having some clarity about what version of cmake they can assume.   Policy [prior to cmake 3.10](https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html#policy-version) was deprecated in cmake 3.31 and policy [prior to cmake 3.5](https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html#policy-version) was removed.  
  
So it seems that cmake 3.10 is _the_ _most_ we could reasonably try to support for boost, but cmake 3.19 is where boost is at currently, in practice.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2025-09-06 11:13:11 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3261887169  

Actually CMake 3.8 (for support of the C++-standard feature) should work for most of Boost   
  
Cobalt requires 3.12, 2-3 libraries have bugs in their CMakeLists that get ignored in 3.19.  
  
So either the maintainer of Cobalt @klemens-morgenstern lowers that if possible or it might really be a good idea to have 3.12 as the minimum in the super-project. However that would exclude users from using an older version when not building Cobalt.     
But as Boost Installation support requires 3.13 anyway I'd expect the impact to be low.

---

## Comment 2

> Username: nigels-com  
> Created_at: 2025-09-06 13:23:51 UTC  
> Updated_at: 2025-09-06 13:26:04 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3262155440  

> Actually CMake 3.8 (for support of the C++-standard feature) should work for most of Boost  
  
The use case I have in mind is building (all of) boost.  
Version 3.8 does not work, and seems unlikely to work, to be honest.  
For most people that want to build boost, making a good impression means succeeding out of the box.  
For superbuild purposes my sense is that a uniform featureful baseline make version is better, and easier to maintain.  
I don't think it's helpful that Cobalt works for 3.12, but depends on things that require 3.19.  
  
I think it's less of a priority to support very old versions of cmake, considering how easy it is to install a modern version of that.

---

## Comment 3

> Username: nigels-com  
> Created_at: 2025-09-06 13:50:31 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3262208121  

I think I now understand your reasoning that a lower version than 3.19 is possible.  
So we're at >= 3.13 (for install purposes) and possibly < 3.19.

---

## Comment 4

> Username: nigels-com  
> Created_at: 2025-09-06 22:30:47 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3263259969  

Thanks to @Flamefire revised this to require cmake 3.15.  
  
It's Json that requires 3.15, as discussed at [boostorg/json#1106](https://github.com/boostorg/json/pull/1106)

---

## Comment 5

> Username: Flamefire  
> Created_at: 2025-09-07 08:52:33 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3263598948  

> The use case I have in mind is building (all of) boost.  
  
But this is not the only use case possible.  
  
>  For most people that want to build boost, making a good impression means succeeding out of the box.  
  
See the "most" here. Boost strives for high compatibility and some libraries do test with compilers as old as available on Ubuntu 16.     
Ubuntu 18 has CMake 3.10 available in the repos and while it is (often) easy to get a newer version one could say it isn't a "good out-of-the-box" experience to needlessly require to install a newer CMake version.  
  
So the minimum required version in the super project needs to be the minimum required version over all Boost libraries for the use case the user just cares about those.  
  
And when all libraries have "correct" CMakeLists the error  
>   CMake 3.15 or higher is required.  You are running version 3.12.0  
  
will appear even without a change to the super project so I don't see much gained by limiting this to 3.15 for users that don't need Boost.JSON.  
  
So I would suggest the above policy:  
- Every library should correctly advertise its required CMake version. If it does not it is a bug and needs to be fixed like any other bug.  
- That version can be raised to the maximum of the minimal CMake version of its dependencies although with care (dependencies can be dropped)  
- The Boost superproject minimal CMake version is the minimum of all minimal CMake versions, the upper bound similarly  
  
Hence I'd keep the current 3.5 minimum and just make sure that each project has a correct CML

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2025-09-07 09:29:04 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3263619108  

How many of those old installations even support C++20? Intuitively that seems unlikely, in which case cobalt doesn't build anything. So I could just do an early return.

---

## Comment 7

> Username: Flamefire  
> Created_at: 2025-09-07 11:55:10 UTC  
> Updated_at: 2025-09-07 11:56:06 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3263720986  

> How many of those old installations even support C++20? Intuitively that seems unlikely, in which case cobalt doesn't build anything. So I could just do an early return.  
  
CMake supports  `cxx_std_20` since version 3.12 so as Cobalt requires C++20 CMake 3.12 is really the correct minimum and no actions is neccessary. Should have checked the source: https://github.com/boostorg/cobalt/blob/77345a996e3e9089b56ca9bdee97c8f18a8154cb/CMakeLists.txt#L6-L7  
  
In CMake you should not "do nothing". Just set the required standard to `cxx_std_20` and let CMake figure out if this would be not possible with the compiler passed by the user.

---

## Comment 8

> Username: nigels-com  
> Created_at: 2025-09-07 13:07:41 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3263764868  

> The Boost superproject minimal CMake version is the minimum of all minimal CMake versions  
  
My view is that boost ought have a *required* cmake version.  
Documented clearly in the release notes.  
And supported by _all_ the boost libraries.  
And guaranteed to work, because there is build and test coverage.  
  
It's normal and expected that ancient cmake versions are not supported _forever_.  
Source code compatibility and CML compatibility are not of the same value, in my opinion.  
  
[cmake-3.15](https://cmake.org/files/v3.15/) is from 2019, six years is long enough.  
And as a reminder, [cmake-3.10](https://cmake.org/files/v3.10/) is from 2017 and deprecated, slated for removal, emitting log spam here and now.

---

## Comment 9

> Username: Flamefire  
> Created_at: 2025-09-07 16:54:36 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3263903949  

> My view is that boost ought have a _required_ cmake version. Documented clearly in the release notes. And supported by _all_ the boost libraries. And guaranteed to work, because there is build and test coverage.  
  
I'd put the CMake version on the same level as the C++ version: Every Boost library decides what it needs as long as it specifies this somewhere.     
They are even connected: A library requiring C++20 requires CMake 3.12+ but a C++98 library is fine with CMake 3.8.     
So why break the build for users that don't need the former library?  
  
> It's normal and expected that ancient cmake versions are not supported _forever_. Source code compatibility and CML compatibility are not of the same value, in my opinion.  
  
Agreed on that. We can be more lenient with the minimal CMake version than with e.g. the C++ standard.  
  
> [cmake-3.15](https://cmake.org/files/v3.15/) is from 2019, six years is long enough. And as a reminder, [cmake-3.10](https://cmake.org/files/v3.10/) is from 2017 and deprecated, slated for removal, emitting log spam here and now.  
  
Which library emits "log spam"? Can be easily fixed without raising the minimum CMake version.  
  
In any case it boils down to  
a) Boost library authors (mostly) decide what they support. Unless there is very good reason I'm not aware of any policy to enforce (non)support for something  
b) Raising the minimum CMake version makes use cases unsupported that were supported right now. I don't see any benefit that justifies that.  
  
The only actionable item I see is ensuring the Super-Project CMake range is the union of all sub-projects (likely 3.8 to 3.23) and adding CI using both the minimum and maximum version to ensure it at least configures.  
  
I'll circulate this in the Slack channel to get the opinion of other authors.

---

## Comment 10

> Username: nigels-com  
> Created_at: 2025-09-07 21:00:01 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3264038325  

Given the concerns.  
I'll think about having the super build warn about pre-cmake-3.15, rather than error, as initially proposed.

---

## Comment 11

> Username: Flamefire  
> Created_at: 2025-09-08 08:27:10 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3265157567  

> I'll think about having the super build warn about pre-cmake-3.15, rather than error, as initially proposed.  
  
What is the motivation of this? I.e. what is the added value of this?  
  
Currently when using less than 3.13 there is a warning that installation requires 3.13.     
When using less than 3.15 **and** building Boost.JSON (which can be excluded or not included) there will be a clear error  
>   CMake 3.15 or higher is required.  You are running version 3.10.0

---

## Comment 12

> Username: nigels-com  
> Created_at: 2025-09-08 10:10:02 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3265574999  

> When using less than 3.15 **and** building Boost.JSON (which can be excluded or not included)  
> there will be a clear error  
  
There will be an error, eventually.  (Unless Boost.JSON was been specifically opted out of)  
But without a warning there no suggestion whether the error is _expected_ or not.  
cmake prior to 3.15 is in less-than-well-supported territory and should at least be discouraged and sign-posted.  (IMHO)  
  
As a bit of an aside I was googling around for cmake best practices as a sanity check.  
[CMake: Best Practices](https://indico.jlab.org/event/420/contributions/7961/attachments/6507/8734/CMakeSandCroundtable.slides.pdf) (circa 2021) for example makes a few remarks relevant to this discussion:  
  
* Boost is starting to support CMake at a reasonable level along with BJam  
* Do: set the highest minimum you can (build systems are hard/ugly enough as it is)  
* Don't: expect a CMake version significantly older than your compiler to work with it (macOS, Windows or CUDA especially)  
* 3.4: The bare minimum. Never set less. (**Still in 2025?**)  
* FetchContent and IMPORTED targets require 3.11  
* C++20 requires 3.12  
* FindPython requires 3.14/3.15  
* Mature CUDA in 3.20  
  
Which broadly resonates with me that support cmake-3.5 (even partially) is at least a bit against the grain of cmake.

---

## Comment 13

> Username: nigels-com  
> Created_at: 2025-09-08 10:18:47 UTC  
> Updated_at: 2025-09-08 10:21:11 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3265609778  

@Flamefire As a bit of an aside, I had a sincere go at supporting old versions for a Boost.Sort stand-alone cmake builds.  
Ironically it's `find_package(Boost)` that seems problematic prior to cmake-3.10.  
And the prospect of testing a whole multi-dimensional matrix of boosts, cmakes and compilers does feel burdensome.  TBH  
But it does seem sufficient for my _original_ purpose of building the Boost.Sort benchmarks with cmake.  
[boostorg/sort#99](https://github.com/boostorg/sort/pull/99/files)  
It's not "as the Romans do in Rome", do let me know, if you can spare any bandwidth.

---

## Comment 14

> Username: nigels-com  
> Created_at: 2025-09-10 01:30:34 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3272881579  

Updated from cmake-3.15 to cmake-3.14 to account for [boostorg/json#1106](https://github.com/boostorg/json/pull/1106) currently in review.

---

## Comment 15

> Username: Flamefire  
> Created_at: 2025-09-10 09:00:48 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3274003162  

> But without a warning there no suggestion whether the error is _expected_ or not.  
  
I don't agree with that. The error is really clear with a line referenced that clearly states the same what the error says: You require a newer CMake version than what you use.  
  
Having the **very same** error message in the Boost super project doesn't change anything but introduces a maintenance burden of having to keep track of changes to this in ALL individual Boost libraries.

---

## Comment 16

> Username: nigels-com  
> Created_at: 2025-09-10 12:05:08 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3274651025  

> > But without a warning there no suggestion whether the error is _expected_ or not.  
>   
> I don't agree with that. The error is really clear with a line referenced that clearly states the  
> same what the error says: You require a newer CMake version than what you use.  
  
The broader problem is that boost seems to have little to say about what version of cmake is _required_, _expected_, _supported_ or even _recommended_.  That seems fine for each library to decide as stand-alone builds.  But does seem problematic for the super-build that accepts (encourages? not clear) cmake version 3.5 but with the proviso that it all depends.    
  
If the first option is to require cmake-3.15, and second option is to warn about cmake-3.15 and before, perhaps a third option is to document what can can be expected with cmake-3.14 and earlier?  
  
I'm interested in establishing some cmake super-project build and test coverage for boost, but it does seem like CMake 3.15 is a workable baseline, without getting caught in these weeds.

---

## Comment 17

> Username: Flamefire  
> Created_at: 2025-10-30 15:33:36 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3468631085  

https://github.com/boostorg/boost/pull/1088 updated that to 3.8  
  
There are now tests in place that catch most issues where a `cmake_minimum_required` specifies a lower version than what is required for the features used.  
  
So the now (hopefully) correct error message like  
> CMake 3.15 or higher is required. You are running version 3.10.0  
  
should offer enough guidance for users.

---

## Comment 18

> Username: nigels-com  
> Created_at: 2025-10-31 10:40:08 UTC  
> Url: https://github.com/boostorg/boost/pull/1081#issuecomment-3472439278  

@Flamefire Thanks for #1088 I do think that is an improvement.

---
