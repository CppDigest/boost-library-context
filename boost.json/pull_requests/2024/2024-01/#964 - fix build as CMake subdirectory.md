# #964 fix build as CMake subdirectory [Merged]

> Username: grisumbras  
> Created at: 2024-01-01 19:00:15 UTC  
> Updated at: 2024-01-01 22:06:55 UTC  
> Merged at: 2024-01-01 21:24:32 UTC  
> Closed at: 2024-01-01 21:24:32 UTC  
> Url: https://github.com/boostorg/json/pull/964  

Also add tests for different CMake use cases

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2024-01-01 19:05:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/964#pullrequestreview-1799853265  

📁 .drone.star

```diff
  38 |   linux_cxx("gcc 10", "g++-10", packages="g++-10", buildtype="boost", buildscript="drone", image="cppalliance/droneubuntu2004:1", environment={'B2_TOOLSET': 'gcc-10', 'B2_CXXSTD': '17,2a', 'DRONE_JOB_UUID': '0716d9708d'}, globalenv=globalenv),
  39 |   linux_cxx("gcc 10 cmake-superproject", "g++-10", packages="g++-10", image=linuxglobalimage, buildtype="cmake-superproject", buildscript="drone", environment={"COMMENT": "cmake-superproject", "CXX": "g++-10"}, globalenv=globalenv),
  40 |+   linux_cxx("gcc 10 cmake-mainproject", "g++-10", packages="g++-10", image=linuxglobalimage, buildtype="cmake-superproject", buildscript="drone", environment={"COMMENT": "cmake-superproject", "CXX": "g++-10"}, globalenv=globalenv),
```

> Username: pdimov  
> Created_at: 2024-01-01 19:05:21 UTC  
> Url: https://github.com/boostorg/json/pull/964#discussion_r1439072618  

`buildtype` should probably be `cmake-mainproject` here?

> Username: grisumbras  
> Created_at: 2024-01-01 19:06:18 UTC  
> Url: https://github.com/boostorg/json/pull/964#discussion_r1439072714  

Oh, lol. True


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2024-01-01 19:05:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/964#pullrequestreview-1799853294  

📁 .drone.star

```diff
  39 |   linux_cxx("gcc 10 cmake-superproject", "g++-10", packages="g++-10", image=linuxglobalimage, buildtype="cmake-superproject", buildscript="drone", environment={"COMMENT": "cmake-superproject", "CXX": "g++-10"}, globalenv=globalenv),
  40 |+   linux_cxx("gcc 10 cmake-mainproject", "g++-10", packages="g++-10", image=linuxglobalimage, buildtype="cmake-superproject", buildscript="drone", environment={"COMMENT": "cmake-superproject", "CXX": "g++-10"}, globalenv=globalenv),
  41 |+   linux_cxx("gcc 10 cmake-subdirectory", "g++-10", packages="g++-10", image=linuxglobalimage, buildtype="cmake-superproject", buildscript="drone", environment={"COMMENT": "cmake-superproject", "CXX": "g++-10"}, globalenv=globalenv),
```

> Username: pdimov  
> Created_at: 2024-01-01 19:05:36 UTC  
> Url: https://github.com/boostorg/json/pull/964#discussion_r1439072654  

Ditto.


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-01-01 22:06:54 UTC  
> Url: https://github.com/boostorg/json/pull/964#issuecomment-1873499314  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/964/pullrequest-condensed-67aa5be.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/964/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/964/pullrequest.html)

---
