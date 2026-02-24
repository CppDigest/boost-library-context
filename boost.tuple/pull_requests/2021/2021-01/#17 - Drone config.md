# #17 Drone config [Open]

> Username: sdarwin  
> Created at: 2021-01-12 17:11:38 UTC  
> Updated at: 2025-05-16 17:57:29 UTC  
> Url: https://github.com/boostorg/tuple/pull/17  

Drone is a continuous integration framework similar to Travis CI. [The C++ Alliance](https://cppalliance.org/) is offering a hosted Drone server for Boost libraries. Please refer to https://github.com/CPPAlliance/drone-ci for more information and instructions.

---

## Comment 1

> Username: saki7  
> Created_at: 2025-05-16 15:07:00 UTC  
> Url: https://github.com/boostorg/tuple/pull/17#issuecomment-2886993639  

I recently became the new maintainer of this repo, and I have no idea whether this 4-year-old PR should be merged or not.

---

## Comment 2

> Username: sdarwin  
> Created_at: 2025-05-16 17:57:28 UTC  
> Url: https://github.com/boostorg/tuple/pull/17#issuecomment-2887357941  

@saki7 you might take newer copies of .drone.star and drone.sh from https://github.com/boostorg/boost-ci   
  
In some sense Drone is redundant, because it provides CI in a similar way to Github Actions.   
  
However, it also offers freebsd, s390x, arm architectures.

---
