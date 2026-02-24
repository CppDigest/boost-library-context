# #4 - namespace alias breaks Jamroot [Closed]

> Username: mgaunard  
> Created at: 2019-05-22 13:39:48 UTC  
> Updated at: 2019-11-01 18:56:32 UTC  
> Closed at: 2019-11-01 18:56:31 UTC  
> Url: https://github.com/boostorg/bcp/issues/4  

Using bcp with namespace alias breaks the Jamroot in at least two locations:  
 - import tools/boost_install/boost-install wrongly renamed  
 - boost-lib rule changes cause DYN_LINK define  to be named wrong, breaking dynamic library exports

---

## Comment 1

> Username: pdimov  
> Created at: 2019-06-17 11:56:34 UTC  
> Url: https://github.com/boostorg/bcp/issues/4#issuecomment-502652292  

This should be fixed by https://github.com/boostorg/boost/commit/d0586e88122f41cd5ac9666db70c37d6f0fc7480.

---

## Comment 2

> Username: firewave  
> Created at: 2019-06-18 17:19:44 UTC  
> Url: https://github.com/boostorg/bcp/issues/4#issuecomment-503230247  

See also https://github.com/boostorg/boost/issues/282

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-11-01 18:56:31 UTC  
> Url: https://github.com/boostorg/bcp/issues/4#issuecomment-548910290  

This appears to now be fixed in develop (at least Peter's CI tests with namespace aliasing are all passing now), so closing down.
