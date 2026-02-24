# #56 Enhance CI, add license and readme, and fix some issues [Merged]

> Username: jeking3  
> Created at: 2018-11-02 18:22:20 UTC  
> Updated at: 2022-06-24 04:14:23 UTC  
> Merged at: 2022-06-24 04:14:22 UTC  
> Closed at: 2022-06-24 04:14:23 UTC  
> Url: https://github.com/boostorg/algorithm/pull/56  

This fixes #52   
  
Note the UBSAN job is expected to fail, it needs to be investigated and resolved.  I added symbol visibility to the exceptions but that didn't make a difference (it should be there anyway).  
  
Since you do not have Travis CI or AppVeyor enabled on this repository, here are the build results:  
  
AppVeyor: https://ci.appveyor.com/project/jeking3/algorithm/builds/20008102  
Travis CI: https://travis-ci.org/jeking3/algorithm/builds/449921988

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-11-02 20:41:30 UTC  
> Url: https://github.com/boostorg/algorithm/pull/56#issuecomment-435502656  

@mclow a few things to do here:  
  
1. Enable Travis CI builds on boostorg/algorithm  
2. Enable AppVeyor builds on boostorg/algorithm  
3. Analyze the UBSAN in the travis result link in the description.  It's a bad vtable in a dtor.  
  
As admin for this repo you must do items 1 and 2 to enable PRs and merges into develop and master to run a build.

---

## Comment 2

> Username: jeking3  
> Created_at: 2019-04-19 20:55:26 UTC  
> Url: https://github.com/boostorg/algorithm/pull/56#issuecomment-485011972  

Wow did you pay for travis-ci.com?

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2019-04-19 21:44:45 UTC  
> Updated_at: 2019-05-03 17:17:14 UTC  
> Url: https://github.com/boostorg/algorithm/pull/56#issuecomment-485021726  

# [Codecov](https://codecov.io/gh/boostorg/algorithm/pull/56?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@9477cd8`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/algorithm/pull/56/graphs/tree.svg?width=650&token=13dgx25S0X&height=150&src=pr)](https://codecov.io/gh/boostorg/algorithm/pull/56?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #56   +/-   ##  
==========================================  
  Coverage           ?   80.75%             
==========================================  
  Files              ?       39             
  Lines              ?      717             
  Branches           ?      236             
==========================================  
  Hits               ?      579             
  Misses             ?       31             
  Partials           ?      107  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/algorithm/pull/56?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/algorithm/apply\_permutation.hpp](https://codecov.io/gh/boostorg/algorithm/pull/56/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9hbGdvcml0aG0vYXBwbHlfcGVybXV0YXRpb24uaHBw) | `100% <ø> (ø)` | |  
| [include/boost/algorithm/hex.hpp](https://codecov.io/gh/boostorg/algorithm/pull/56/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9hbGdvcml0aG0vaGV4LmhwcA==) | `75.36% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/algorithm/pull/56?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/algorithm/pull/56?src=pr&el=footer). Last update [9477cd8...ac1713e](https://codecov.io/gh/boostorg/algorithm/pull/56?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: jeking3  
> Created_at: 2019-05-05 18:05:35 UTC  
> Url: https://github.com/boostorg/algorithm/pull/56#issuecomment-489449225  

This was passing on my fork...  
https://github.com/jeking3/algorithm/pull/2

---

## Comment 5

> Username: jgopel  
> Created_at: 2022-06-05 13:55:34 UTC  
> Url: https://github.com/boostorg/algorithm/pull/56#issuecomment-1146809424  

What's the status on this? I think that landing a change along these lines would help me a lot, as I could offload most of my `b2` permutations to CI.

---
