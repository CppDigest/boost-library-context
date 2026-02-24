# #62 Tidy up and simplify GitHub Actions YAML [Merged]

> Username: mloskot  
> Created at: 2020-03-18 09:41:52 UTC  
> Updated at: 2020-03-18 18:03:19 UTC  
> Merged at: 2020-03-18 18:03:15 UTC  
> Closed at: 2020-03-18 18:03:15 UTC  
> Url: https://github.com/boostorg/json/pull/62  

Apply @Flamefire's suggestions  
  
https://github.com/CPPAlliance/json/pull/60#discussion_r394144512  
  
https://github.com/CPPAlliance/json/pull/60#discussion_r394143790  
  
https://github.com/CPPAlliance/json/pull/60#discussion_r394211642

---

## Review 1 [Commented]

> Username: Flamefire  
> Created_at: 2020-03-18 09:47:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/62#pullrequestreview-376704469  

📁 .github/workflows/cmake-with-boost-stage.yml

```diff
  27 |           echo "::set-env name=BOOST_ROOT::$GITHUB_WORKSPACE/boost-root"
  28 |-           echo "::set-output name=boost_self::$(basename $GITHUB_WORKSPACE)"
  29 |-           echo "::set-output name=boost_root::$GITHUB_WORKSPACE/boost-root"
```

> Username: Flamefire  
> Created_at: 2020-03-18 09:47:00 UTC  
> Updated_at: 2020-03-18 09:59:05 UTC  
> Url: https://github.com/boostorg/json/pull/62#discussion_r394219522  

You can now also remove the `id:`


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-03-18 09:59:09 UTC  
> Updated_at: 2020-03-18 10:37:48 UTC  
> Url: https://github.com/boostorg/json/pull/62#issuecomment-600528468  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/62?src=pr&el=h1) Report  
> Merging [#62](https://codecov.io/gh/CPPAlliance/json/pull/62?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/5c8c7e1d2106b5dd61491d5cc5f9cbdb44527c11&el=desc) will **not change** coverage by `%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/62/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/62?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #62   +/-   ##  
========================================  
  Coverage    99.09%   99.09%             
========================================  
  Files           57       57             
  Lines         4861     4861             
========================================  
  Hits          4817     4817             
  Misses          44       44             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/62?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/62?src=pr&el=footer). Last update [5c8c7e1...0ae44de](https://codecov.io/gh/CPPAlliance/json/pull/62?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
