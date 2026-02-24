# #102 ci(enhancement): Do not trigger Github Actions for doc or Readme changes [Merged]

> Username: coder3101  
> Created at: 2020-08-06 05:17:44 UTC  
> Updated at: 2020-08-12 14:41:33 UTC  
> Merged at: 2020-08-12 14:41:33 UTC  
> Closed at: 2020-08-12 14:41:33 UTC  
> Url: https://github.com/boostorg/ublas/pull/102  

As pointed out by @jwakely in this comment https://github.com/boostorg/ublas/pull/99#issuecomment-669201622 it is not wise to trigger CI for changes in docs or any other non-code related changes. This commit relaxes the trigger restriction on PR and Push event so that they are only triggered when at least one file changed which does not belong to the below list:  
  
- `Markdown` changed (anything ending with .md)  
-  Anything inside `doc` subdirectory.

---

## Comment 1

> Username: coder3101  
> Created_at: 2020-08-06 05:22:37 UTC  
> Url: https://github.com/boostorg/ublas/pull/102#issuecomment-669703822  

Travis and Appveyor will still trigger. They are legacy CI we will drop in future, so let them get triggered until their removal.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-08-06 05:27:35 UTC  
> Url: https://github.com/boostorg/ublas/pull/102#issuecomment-669707982  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/102?src=pr&el=h1) Report  
> Merging [#102](https://codecov.io/gh/boostorg/ublas/pull/102?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/ublas/commit/a31e5cffa85f58b64a39fa7c4a1bd3bd9228b069&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/102/graphs/tree.svg?width=650&height=150&src=pr&token=CShoBArRWq)](https://codecov.io/gh/boostorg/ublas/pull/102?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #102   +/-   ##  
========================================  
  Coverage    95.33%   95.33%             
========================================  
  Files           22       22             
  Lines         1223     1223             
========================================  
  Hits          1166     1166             
  Misses          57       57             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/102?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/102?src=pr&el=footer). Last update [a31e5cf...60be7a0](https://codecov.io/gh/boostorg/ublas/pull/102?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: bassoy  
> Created_at: 2020-08-12 06:22:46 UTC  
> Url: https://github.com/boostorg/ublas/pull/102#issuecomment-672634461  

@coder3101   
I think it would make sense to have a Github-Action for the CMAKE on a windows and linux machine.

---

## Comment 4

> Username: coder3101  
> Created_at: 2020-08-12 06:28:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/102#issuecomment-672636613  

Okay, I will add it after my CMake PR is merged.

---
