# #79 Update CI scripts [Merged]

> Username: Flast  
> Created at: 2019-01-16 09:00:02 UTC  
> Updated at: 2019-01-16 23:51:53 UTC  
> Merged at: 2019-01-16 23:51:50 UTC  
> Closed at: 2019-01-16 23:51:50 UTC  
> Url: https://github.com/boostorg/phoenix/pull/79  



---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2019-01-16 14:43:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/phoenix/pull/79#pullrequestreview-193169497  

📁 .travis.yml

```diff
 472 |   - git submodule init libs/headers tools/boost_install tools/build
 348 |-   - git submodule update
 473 |+   - git submodule update --jobs `(nproc || sysctl -n hw.ncpu)`
```

> Username: Kojoley  
> Created_at: 2019-01-16 14:43:14 UTC  
> Updated_at: 2019-01-16 23:34:08 UTC  
> Url: https://github.com/boostorg/phoenix/pull/79#discussion_r248307812  

You can set this to something like 10 because it is not CPU bound, but network IO latency bound.


---
