# #45 Drone config [Closed]

> Username: sdarwin  
> Created at: 2021-01-26 15:33:03 UTC  
> Updated at: 2022-03-06 22:10:12 UTC  
> Closed at: 2022-03-06 22:10:12 UTC  
> Url: https://github.com/boostorg/atomic/pull/45  

Drone is a continuous integration framework similar to Travis CI. [The C++ Alliance](https://cppalliance.org/) is offering a hosted Drone server for Boost libraries. Please refer to https://github.com/CPPAlliance/drone-ci for more information and instructions.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2021-01-26 16:09:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/atomic/pull/45#pullrequestreview-576509439  

📁 .drone.star

```diff
  29 |+   linux_cxx("TOOLSET=gcc COMPILER=g++ CXXSTD64=03,11,14,17 Job 12", "g++", packages="", buildtype="boost", buildscript="drone", image="cppalliance/droneubuntu2004:1", environment={'TOOLSET': 'gcc', 'COMPILER': 'g++', 'CXXSTD64': '03,11,14,17,03-gnu,11-gnu,14-gnu,17-gnu', 'DRONE_JOB_UUID': '7b52009b64'}, globalenv=globalenv),
  30 |+   linux_cxx("TOOLSET=gcc COMPILER=g++ CXXSTD64=03,11,14,17 Job 13", "g++", packages="", buildtype="boost", buildscript="drone", image="cppalliance/droneubuntu2004:1", environment={'TOOLSET': 'gcc', 'COMPILER': 'g++', 'CXXSTD64': '03,11,14,17,03-gnu,11-gnu,14-gnu,17-gnu', 'DRONE_JOB_UUID': 'bd307a3ec3'}, globalenv=globalenv),
  31 |+   linux_cxx("TOOLSET=gcc COMPILER=g++ CXXSTD64=03,11,14,17 Job 14", "g++", packages="", buildtype="boost", buildscript="drone", image="cppalliance/droneubuntu2004:1", environment={'TOOLSET': 'gcc', 'COMPILER': 'g++', 'CXXSTD64': '03,11,14,17,03-gnu,11-gnu,14-gnu,17-gnu', 'DRONE_JOB_UUID': 'fa35e19212'}, globalenv=globalenv),
```

> Username: Lastique  
> Created_at: 2021-01-26 16:09:09 UTC  
> Url: https://github.com/boostorg/atomic/pull/45#discussion_r564634935  

These three are supposed to run on non-x86 CPUs, and I don't see this reflected in the arguments.


---

## Comment 2

> Username: Lastique  
> Created_at: 2021-01-26 16:09:44 UTC  
> Url: https://github.com/boostorg/atomic/pull/45#issuecomment-767649801  

I don't think I will merge this as it has a dependency on boost-ci.

---

## Comment 3

> Username: sdarwin  
> Created_at: 2021-01-26 19:13:20 UTC  
> Url: https://github.com/boostorg/atomic/pull/45#issuecomment-767765759  

> These three are supposed to run on non-x86 CPUs,  
  
It's a good suggestion, many of the boost developers are discussing tests on alternative CPU architectures.   This could be the next enhancement for drone.  
  
> I don't think I will merge this as it has a dependency on boost-ci.  
  
Ok, that is your decision.    
  
 The link  https://github.com/CPPAlliance/drone-ci includes instructions for removing dependencies, although not recommended because... how will features such as new CPU architectures get added?  Much of the logic (although not all) will go into the main functions.star file.

---

## Comment 4

> Username: Lastique  
> Created_at: 2021-01-26 20:02:29 UTC  
> Updated_at: 2021-01-26 20:03:44 UTC  
> Url: https://github.com/boostorg/atomic/pull/45#issuecomment-767793420  

> how will features such as new CPU architectures get added?  
  
I would like to be in control of what and how is tested, and when support for new compilers is added, and which compiler flags to use, etc. This is why understanding how to support CI configs is important for me.

---

## Comment 5

> Username: sdarwin  
> Created_at: 2021-01-26 20:08:54 UTC  
> Url: https://github.com/boostorg/atomic/pull/45#issuecomment-767796925  

> I would like to be in control of what and how is tested, and when support for new compilers is added, and which compiler flags to use, etc. This is why understanding how to support CI configs is important for me.  
  
Most of the settings are in .drone.star, in your local repo, which means you do control them.

---

## Comment 6

> Username: Lastique  
> Created_at: 2022-03-06 22:10:12 UTC  
> Url: https://github.com/boostorg/atomic/pull/45#issuecomment-1060048777  

I don't think I will be enabling Drone CI - GitHub Actions and AppVeyor cover our testing. Thanks for the submission though.

---
