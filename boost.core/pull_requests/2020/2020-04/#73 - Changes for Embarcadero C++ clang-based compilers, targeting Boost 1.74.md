# #73 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74 [Closed]

> Username: eldiener  
> Created at: 2020-04-08 16:06:46 UTC  
> Updated at: 2020-05-08 14:16:00 UTC  
> Closed at: 2020-05-08 08:42:28 UTC  
> Url: https://github.com/boostorg/core/pull/73  



---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2020-05-08 08:31:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/73#pullrequestreview-408087198  

📁 .travis.yml

```diff
 277 |       compiler: clang++-10
 278 |-       env: TOOLSET=clang COMPILER=clang++-10 CXXSTD=03,11,14,17,20
 278 |+       env: TOOLSET=clang COMPILER=clang++-10 CXXSTD=03,11,14,17,2a
```

> Username: Lastique  
> Created_at: 2020-05-08 08:31:33 UTC  
> Updated_at: 2020-05-08 08:31:34 UTC  
> Url: https://github.com/boostorg/core/pull/73#discussion_r422017904  

Please, revert this. Boost.Build has been updated to support `cxxstd=20`.

> Username: eldiener  
> Created_at: 2020-05-08 08:36:33 UTC  
> Updated_at: 2020-05-08 08:36:34 UTC  
> Url: https://github.com/boostorg/core/pull/73#discussion_r422020151  

Yet the clang-10.0 test was failing because of the use of '20' instead of '2a'. That is why I made the change. Notice that all the other clang tests have '2a' and not '20' and succeeded. Are you saying that if I revert this the clang-10.0 test, which has '20' instead of '2a' will succeed when this PR is tested again ?

> Username: Lastique  
> Created_at: 2020-05-08 08:46:22 UTC  
> Url: https://github.com/boostorg/core/pull/73#discussion_r422024560  

clang 10 is the first version that supports `-std=c++20`, that's why the others use 2a. Boost.Build did not have support for `cxxstd=20` until https://github.com/boostorg/build/pull/592 was merged recently. If you restarted the CI, that error would not happen.

> Username: pdimov  
> Created_at: 2020-05-08 13:55:01 UTC  
> Url: https://github.com/boostorg/core/pull/73#discussion_r422156640  

> Boost.Build has been updated to support cxxstd=20.  
  
Not on master, which means that when we merge to master we'll get Travis failures again.  
  
Rene only merges to master when he does a release.

> Username: Lastique  
> Created_at: 2020-05-08 14:16:00 UTC  
> Url: https://github.com/boostorg/core/pull/73#discussion_r422168315  

Ok, I will change to 2a then.


---

## Comment 2

> Username: Lastique  
> Created_at: 2020-05-08 08:42:28 UTC  
> Url: https://github.com/boostorg/core/pull/73#issuecomment-625709758  

Merged just the essential commit.

---
