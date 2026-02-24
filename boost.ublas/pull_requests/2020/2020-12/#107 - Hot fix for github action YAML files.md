# #107 Hot fix for github action YAML files [Merged]

> Username: bassoy  
> Created at: 2020-12-10 17:29:55 UTC  
> Updated at: 2021-01-04 20:10:54 UTC  
> Merged at: 2020-12-10 18:01:03 UTC  
> Closed at: 2020-12-10 18:01:03 UTC  
> Url: https://github.com/boostorg/ublas/pull/107  

This is a hot fix for github action YAML files.   
  
Fixed the deprecated set-env commands, see [github blog](https://github.blog/changelog/2020-10-01-github-actions-deprecating-set-env-and-add-path-commands/).

---

## Comment 1

> Username: github-actions[bot]  
> Created_at: 2020-12-10 17:34:36 UTC  
> Url: https://github.com/boostorg/ublas/pull/107#issuecomment-742674912  

This Pull request Passed all of clang-tidy tests. :+1:

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-12-10 17:38:09 UTC  
> Url: https://github.com/boostorg/ublas/pull/107#issuecomment-742677080  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/107?src=pr&el=h1) Report  
> Merging [#107](https://codecov.io/gh/boostorg/ublas/pull/107?src=pr&el=desc) (daa5a72) into [develop](https://codecov.io/gh/boostorg/ublas/commit/47a314dd01d1adacaaf7265c7c644f8f70afd402?el=desc) (47a314d) will **increase** coverage by `26.97%`.  
> The diff coverage is `95.42%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/107/graphs/tree.svg?width=650&height=150&src=pr&token=CShoBArRWq)](https://codecov.io/gh/boostorg/ublas/pull/107?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff              @@  
##           develop     #107       +/-   ##  
============================================  
+ Coverage    67.57%   94.54%   +26.97%       
============================================  
  Files           33       19       -14       
  Lines         1437     1192      -245       
  Branches       534        0      -534       
============================================  
+ Hits           971     1127      +156       
+ Misses          69       65        -4       
+ Partials       397        0      -397       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/ublas/pull/107?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/numeric/ublas/tensor/multi\_index.hpp](https://codecov.io/gh/boostorg/ublas/pull/107/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9tdWx0aV9pbmRleC5ocHA=) | `100.00% <ø> (ø)` | |  
| [...lude/boost/numeric/ublas/tensor/multiplication.hpp](https://codecov.io/gh/boostorg/ublas/pull/107/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9tdWx0aXBsaWNhdGlvbi5ocHA=) | `85.83% <ø> (+20.83%)` | :arrow_up: |  
| [.../boost/numeric/ublas/tensor/fixed\_rank\_strides.hpp](https://codecov.io/gh/boostorg/ublas/pull/107/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9maXhlZF9yYW5rX3N0cmlkZXMuaHBw) | `94.28% <85.71%> (+15.71%)` | :arrow_up: |  
| [...ude/boost/numeric/ublas/tensor/dynamic\_strides.hpp](https://codecov.io/gh/boostorg/ublas/pull/107/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9keW5hbWljX3N0cmlkZXMuaHBw) | `94.59% <86.66%> (+8.54%)` | :arrow_up: |  
| [.../boost/numeric/ublas/tensor/fixed\_rank\_extents.hpp](https://codecov.io/gh/boostorg/ublas/pull/107/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9maXhlZF9yYW5rX2V4dGVudHMuaHBw) | `96.15% <93.10%> (+19.01%)` | :arrow_up: |  
| [include/boost/numeric/ublas/tensor/tensor\_core.hpp](https://codecov.io/gh/boostorg/ublas/pull/107/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci90ZW5zb3JfY29yZS5ocHA=) | `93.18% <93.18%> (ø)` | |  
| [include/boost/numeric/ublas/tensor/functions.hpp](https://codecov.io/gh/boostorg/ublas/pull/107/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9mdW5jdGlvbnMuaHBw) | `94.94% <98.33%> (+50.79%)` | :arrow_up: |  
| [include/boost/numeric/ublas/tensor/algorithms.hpp](https://codecov.io/gh/boostorg/ublas/pull/107/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9hbGdvcml0aG1zLmhwcA==) | `99.09% <100.00%> (+45.58%)` | :arrow_up: |  
| [...ude/boost/numeric/ublas/tensor/dynamic\_extents.hpp](https://codecov.io/gh/boostorg/ublas/pull/107/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9keW5hbWljX2V4dGVudHMuaHBw) | `100.00% <100.00%> (+16.27%)` | :arrow_up: |  
| [include/boost/numeric/ublas/tensor/expression.hpp](https://codecov.io/gh/boostorg/ublas/pull/107/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9leHByZXNzaW9uLmhwcA==) | `100.00% <100.00%> (+16.66%)` | :arrow_up: |  
| ... and [30 more](https://codecov.io/gh/boostorg/ublas/pull/107/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/107?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/107?src=pr&el=footer). Last update [799ef70...f68b8f4](https://codecov.io/gh/boostorg/ublas/pull/107?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: bassoy  
> Created_at: 2020-12-10 18:00:53 UTC  
> Url: https://github.com/boostorg/ublas/pull/107#issuecomment-742691985  

I am merging this one to develop as it does only affect github actions.

---
