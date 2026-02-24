# #16 - Allow using boostdep with older boost versions [Open]

> Username: Flamefire  
> Created at: 2020-06-09 14:31:03 UTC  
> Updated at: 2020-06-09 15:03:35 UTC  
> Url: https://github.com/boostorg/boostdep/issues/16  

In an effort of migrating from bintray downloads (with caching) on CI I wanted to use boostdep with older boost versions (Use case: Install a subset of the libraries)  
  
The problem I'm running into is that some modules didn't exist yet. So for example FileSystem includes `boost/functional/hash_fwd.hpp` which is mapped to `container_hash` via exceptions.txt. But that module doesn't exist so the subsequent `git submodule update` fails.  
  
A potential (partial) solution is to check the modules in the exceptions file via `is_module` and skip the following mappings if the module doesn't exist.  
  
Downside: Moved headers such as in https://github.com/boostorg/boostdep/commit/9d5890819358d25d4482120650bc38e1199d4ebc#diff-6935182ccded16172f0fa2c6a34a6bb4 or https://github.com/boostorg/boostdep/commit/b87f4a3bdc316c354121057508d45c9c161ca28d#diff-6935182ccded16172f0fa2c6a34a6bb4 won't be correct.  
  
But at least it runs through which is a bit better and such known things could be manually specified by the user

---

## Comment 1

> Username: pdimov  
> Created at: 2020-06-09 15:00:01 UTC  
> Url: https://github.com/boostorg/boostdep/issues/16#issuecomment-641354116  

Why not use `exceptions.txt` from the appropriate Boost release?

---

## Comment 2

> Username: Flamefire  
> Created at: 2020-06-09 15:03:35 UTC  
> Url: https://github.com/boostorg/boostdep/issues/16#issuecomment-641356329  

Yes this works (if one exists for a release which is true for quite some time). Just thought it might be possible to improve by not requiring the user (of boostdep) to fetch an additional file but to instead combine all into 1
