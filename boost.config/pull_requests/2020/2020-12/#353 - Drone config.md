# #353 Drone config [Merged]

> Username: sdarwin  
> Created at: 2020-12-29 18:03:25 UTC  
> Updated at: 2020-12-29 19:20:06 UTC  
> Merged at: 2020-12-29 19:20:06 UTC  
> Closed at: 2020-12-29 19:20:06 UTC  
> Url: https://github.com/boostorg/config/pull/353  

Drone is a continuous integration framework similar to Travis CI. [The C++ Alliance](https://cppalliance.org/) is offering a hosted Drone server for Boost libraries. Please refer to https://github.com/CPPAlliance/drone-ci for more information and instructions.

---

## Comment 1

> Username: sdarwin  
> Created_at: 2020-12-29 18:50:27 UTC  
> Updated_at: 2020-12-29 18:50:40 UTC  
> Url: https://github.com/boostorg/config/pull/353#issuecomment-752202571  

> Trigger rebuild    
  
It may require the .drone.star file to be present in the target branch. i.e. "develop".

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-12-29 19:19:57 UTC  
> Url: https://github.com/boostorg/config/pull/353#issuecomment-752212454  

Closing and re-opening usually re-triggers CI, but apparently not :(  
  
Will tentatively merge and see what happens.

---
