# #3 - Missing hash_value overloads for C++11 and later containers [Closed]

> Username: zamazan4ik  
> Created at: 2018-11-13 09:24:35 UTC  
> Updated at: 2022-10-27 11:55:37 UTC  
> Closed at: 2022-10-27 11:55:37 UTC  
> Url: https://github.com/boostorg/container_hash/issues/3  

Hello.  
  
In Boost.ContainerHash library there are no `hash_value` overloads for all containers with PMR allocator. Also overloads for unordered_(multi)set/map are missing.  
  
Do you plan to add it later?

---

## Comment 1

> Username: pdimov  
> Created at: 2022-09-19 10:04:39 UTC  
> Url: https://github.com/boostorg/container_hash/issues/3#issuecomment-1250818386  

These should all be supported now (on develop).

---

## Comment 2

> Username: pdimov  
> Created at: 2022-10-27 11:55:37 UTC  
> Url: https://github.com/boostorg/container_hash/issues/3#issuecomment-1293415769  

The develop branch has been merged now, so this should be resolved.
