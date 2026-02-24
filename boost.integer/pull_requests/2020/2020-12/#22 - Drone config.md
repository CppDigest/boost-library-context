# #22 Drone config [Merged]

> Username: sdarwin  
> Created at: 2020-12-30 14:03:27 UTC  
> Updated at: 2021-01-11 19:51:27 UTC  
> Merged at: 2021-01-07 19:40:23 UTC  
> Closed at: 2021-01-07 19:40:23 UTC  
> Url: https://github.com/boostorg/integer/pull/22  

Drone is a continuous integration framework similar to Travis CI. [The C++ Alliance](https://cppalliance.org/) is offering a hosted Drone server for Boost libraries. Please refer to https://github.com/CPPAlliance/drone-ci for more information and instructions.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2021-01-11 11:46:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/integer/pull/22#pullrequestreview-565280842  

📁 .drone.star

```diff
  25 |+   linux_cxx("TOOLSET=gcc COMPILER=g++-8 CXXSTD=03,11,14,17 Job 8", "g++-8", packages="g++-8", buildtype="boost", image="cppalliance/droneubuntu1604:1", environment={'TOOLSET': 'gcc', 'COMPILER': 'g++-8', 'CXXSTD': '03,11,14,17,2a', 'DRONE_JOB_UUID': 'fe5dbbcea5'}, globalenv=globalenv),
  26 |+   linux_cxx("TOOLSET=gcc COMPILER=g++-9 CXXSTD=03,11,14,17 Job 9", "g++-9", packages="g++-9", buildtype="boost", image="cppalliance/droneubuntu1804:1", environment={'TOOLSET': 'gcc', 'COMPILER': 'g++-9', 'CXXSTD': '03,11,14,17,2a', 'DRONE_JOB_UUID': '0ade7c2cf9'}, globalenv=globalenv),
  27 |+   linux_cxx("TOOLSET=gcc COMPILER=g++-10 CXXSTD=03,11,14,1 Job 10", "g++-10", packages="g++-10", buildtype="boost", image="cppalliance/droneubuntu1804:1", environment={'TOOLSET': 'gcc', 'COMPILER': 'g++-10', 'CXXSTD': '03,11,14,17,20', 'DRONE_JOB_UUID': 'b1d5781111'}, globalenv=globalenv),
```

> Username: Lastique  
> Created_at: 2021-01-11 11:46:46 UTC  
> Updated_at: 2021-01-11 11:49:05 UTC  
> Url: https://github.com/boostorg/integer/pull/22#discussion_r554991458  

Why is CXXSTD truncated in the first argument of `linux_cxx`?

> Username: jzmaddock  
> Created_at: 2021-01-11 13:12:27 UTC  
> Url: https://github.com/boostorg/integer/pull/22#discussion_r555036235  

I wondered that too: but I think this is just the description that goes on the website.

> Username: Lastique  
> Created_at: 2021-01-11 13:22:53 UTC  
> Updated_at: 2021-01-11 13:22:54 UTC  
> Url: https://github.com/boostorg/integer/pull/22#discussion_r555042256  

Even if so, I would've preferred to have it intact.

> Username: jzmaddock  
> Created_at: 2021-01-11 17:37:30 UTC  
> Url: https://github.com/boostorg/integer/pull/22#discussion_r555223343  

@sdarwin : any reason for this?

> Username: sdarwin  
> Created_at: 2021-01-11 17:50:45 UTC  
> Updated_at: 2021-01-11 17:50:46 UTC  
> Url: https://github.com/boostorg/integer/pull/22#discussion_r555231893  

@Lastique @jzmaddock your feedback is great.  I have updated the docs to clarify:  
  
"""  
name - The name field will be displayed for each job in the Drone UI (drone.cpp.al). You are encouraged to adjust the name to be more descriptive. When the first batch of .drone.star files was created using an automated script, the name was based on the environment variables in .travis.yml. The original .travis.yml file doesn't include a name, so it had to be invented. The script truncates the name at around 50 characters, since a long name will not be displayed on the web page.  
"""


---

## Comment 2

> Username: Lastique  
> Created_at: 2021-01-11 11:48:39 UTC  
> Url: https://github.com/boostorg/integer/pull/22#issuecomment-757900807  

Frankly, I would have preferred if this didn't depend on boost-ci. And I also have no idea how to maintain this. I expressed my thoughts in https://github.com/boostorg/core/pull/84.

---

## Comment 3

> Username: glenfe  
> Created_at: 2021-01-11 19:45:15 UTC  
> Url: https://github.com/boostorg/integer/pull/22#issuecomment-758180764  

For the repositories I maintain,  I want to have a version that does not depend on boost-ci (just like my travis, appveyor, and github actions configurations).

---

## Comment 4

> Username: sdarwin  
> Created_at: 2021-01-11 19:51:27 UTC  
> Url: https://github.com/boostorg/integer/pull/22#issuecomment-758184207  

@glenfe, instructions for removing the dependency have been added: https://github.com/CPPAlliance/drone-ci/blob/master/docs/drone-ci.md

---
