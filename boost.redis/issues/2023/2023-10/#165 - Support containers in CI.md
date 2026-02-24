# #165 - Support containers in CI [Closed]

> Username: mzimbres  
> Created at: 2023-10-28 11:08:55 UTC  
> Updated at: 2023-11-12 14:08:43 UTC  
> Closed at: 2023-11-12 14:08:43 UTC  
> Url: https://github.com/boostorg/redis/issues/165  

The develop branch can be merged in master without adding support for containerization. The is the recommendation Peter did in cpplang  
  
> try using a container instead of ubuntu-22.04 directly  
  
> you can look at https://github.com/boostorg/variant2/commit/78a974429ac8ff631e956f3a08f401f4c9a09ce6 and https://github.com/boostorg/variant2/commit/362224a009e5a009efdc88445ab50b31eb474066 as a guide

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-10-28 11:29:35 UTC  
> Url: https://github.com/boostorg/redis/issues/165#issuecomment-1783784401  

> once your ci.yml file is set up to support container:, you can do this https://github.com/boostorg/ratio/commit/a948c0fac43dce2953e4ada607a0f523e24c4da7
