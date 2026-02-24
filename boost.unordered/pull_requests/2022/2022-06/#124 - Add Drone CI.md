# #124 Add Drone CI [Closed]

> Username: Flamefire  
> Created at: 2022-06-10 09:59:41 UTC  
> Updated at: 2024-12-09 10:42:12 UTC  
> Closed at: 2022-06-11 15:12:39 UTC  
> Url: https://github.com/boostorg/unordered/pull/124  

Supersedes and closes #27  
  
I simply C&P the config file from Boost.CI, so not sure if everything passes.  
  
Notes on Drone (if not already done, can you do that please @cmazakas ?):  
  
> - To activate Drone, visit https://drone.cpp.al/. Authorize Drone: Click the "Authorize cppalliance-drone" button. Sync repositories: Click the "sync" button. A list of repositories will appear. For the relevant repo, click and then choose "Activate Repository". In the settings page, change Configuration from .drone.yml to .drone.star. "Save".  
>  
> More pointers about Drone:  
>  
> -    "asan" jobs require elevated privileges. Contact an administrator or open an issue at [drone-ci](https://github.com/CPPAlliance/drone-ci) to set your drone repository to "Trusted".  
> -    If not using asan, simply remove the jobs.  
  
  
CC @tobias-loew. Just want to make sure your work doesn't get forgotten.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-10 12:27:09 UTC  
> Updated_at: 2022-06-10 20:36:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/124#issuecomment-1152305779  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/124?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#124](https://codecov.io/gh/boostorg/unordered/pull/124?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6446e0e) into [develop](https://codecov.io/gh/boostorg/unordered/commit/23f15947d32e83e3e4e96cb00d52ebbd00355794?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (23f1594) will **not change** coverage.  
> The diff coverage is `84.61%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/124/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/124?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #124   +/-   ##  
========================================  
  Coverage    97.30%   97.30%             
========================================  
  Files           73       73             
  Lines         9790     9790             
========================================  
  Hits          9526     9526             
  Misses         264      264             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/124?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/objects/test.hpp](https://codecov.io/gh/boostorg/unordered/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9vYmplY3RzL3Rlc3QuaHBw) | `94.76% <80.00%> (ø)` | |  
| [test/objects/exception.hpp](https://codecov.io/gh/boostorg/unordered/pull/124/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9vYmplY3RzL2V4Y2VwdGlvbi5ocHA=) | `96.96% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/124?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/124?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [23f1594...6446e0e](https://codecov.io/gh/boostorg/unordered/pull/124?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: Flamefire  
> Created_at: 2022-06-10 17:59:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/124#issuecomment-1152605392  

@cmazakas For Codecov on Drone you need to copy the "Repository Upload Token" from the settings page of the repo on [Codecov](https://codecov.io) and use it to create a new secret named `codecov_token` on the settings page of the repo on [Drone](https://drone.cpp.al).  
  
Besides that it passes: https://drone.cpp.al/Flamefire/unordered/3 (now: https://drone.cpp.al/Flamefire/unordered/4)  
  
And it seems the codecov bot is now running. It detects that there is some dead code in `test/objects/test.hpp`: The int-functor part of the `hash` class is only called on a default constructed `hash`-object.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-06-11 12:04:58 UTC  
> Url: https://github.com/boostorg/unordered/pull/124#issuecomment-1152912059  

What's the purpose of this? The benefit of Drone is that we can use it to cover configurations GHA lacks (GCC 4.4, 4.6, 4.7, ARM, s390x, etc), but this PR doesn't do this, it just duplicates the same configs already covered.

---

## Comment 4

> Username: pdimov  
> Created_at: 2022-06-11 12:20:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/124#issuecomment-1152916438  

Here's my Drone variation: https://github.com/boostorg/system/blob/develop/.drone.jsonnet (self-contained as usual)  
  
I prefer jsonnet, see if you like it better. We didn't have s390x yet when I wrote it, but it's an important configuration because it's big endian.

---

## Comment 5

> Username: Flamefire  
> Created_at: 2022-06-11 12:24:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/124#issuecomment-1152916978  

Redundancy, as that was also a reason to add Drone.  
I just used the config as it is on boost-ci. If there are more supported and useful then those should be added to boost-ci, which is the blueprint / testbed for the other repos to copy from. I don't think it is feasible to go looking in random boost repos for a good config.  
So if yours is better, can you add/replace it on boost-ci with instructions how to use it?

---

## Comment 6

> Username: pdimov  
> Created_at: 2022-06-11 12:37:39 UTC  
> Url: https://github.com/boostorg/unordered/pull/124#issuecomment-1152918636  

drone.cpp.al has limited capacity and I don't think we should be carpet bombing it with jobs we don't actually need because they are already covered elsewhere. This will ruin it for everyone.

---

## Comment 7

> Username: Flamefire  
> Created_at: 2022-06-11 15:12:39 UTC  
> Url: https://github.com/boostorg/unordered/pull/124#issuecomment-1152948083  

Sure. Closing this then.

---
