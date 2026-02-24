# #21 Update YAML scripts [Closed]

> Username: CromwellEnage  
> Created at: 2018-12-26 08:21:14 UTC  
> Updated at: 2019-02-18 21:44:17 UTC  
> Closed at: 2019-02-18 21:44:17 UTC  
> Url: https://github.com/boostorg/multi_array/pull/21  

* Add git statements for b2 cmake support.  
* Remove "sudo: false" statement due to being deprecated by Travis CI.

---

## Comment 1

> Username: glenfe  
> Created_at: 2019-02-18 21:44:17 UTC  
> Url: https://github.com/boostorg/multi_array/pull/21#issuecomment-464891060  

This shouldn't be necessary; our current configurations for Travis and Appveyor that use depinst should continue to work after the changes that introduced tools/boost_install.

---
