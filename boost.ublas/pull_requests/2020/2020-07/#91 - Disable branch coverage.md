# #91 Disable branch coverage [Merged]

> Username: coder3101  
> Created at: 2020-07-11 16:51:33 UTC  
> Updated at: 2020-07-12 13:21:10 UTC  
> Merged at: 2020-07-12 13:21:10 UTC  
> Closed at: 2020-07-12 13:21:10 UTC  
> Url: https://github.com/boostorg/ublas/pull/91  

Branch coverage needs to be explicitly enabled and is not the defaault configuration of `lcov` . It is better to disable branch coverage because then we will have only 2 things for each line in coverage report. Either a line is hit or a miss. This is even the default behaviour of `llvm-cov` for coverage reporting. For reference check [this](https://codecov.io/gh/tzlaine/text/tree/master/boost/text) Zach's Library for Unicode which recently got partially approved for Boost. This library also uses default coverage settings.   
  
**After this commit coverage will be 95%**

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-07-11 17:03:32 UTC  
> Url: https://github.com/boostorg/ublas/pull/91#issuecomment-657095685  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/91?src=pr&el=h1) Report  
> Merging [#91](https://codecov.io/gh/boostorg/ublas/pull/91?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/ublas/commit/0584e2abaa587b24ddd7227a306f281c08228603&el=desc) will **increase** coverage by `24.36%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/91/graphs/tree.svg?width=650&height=150&src=pr&token=CShoBArRWq)](https://codecov.io/gh/boostorg/ublas/pull/91?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff              @@  
##           develop      #91       +/-   ##  
============================================  
+ Coverage    70.97%   95.33%   +24.36%       
============================================  
  Files           22       22                 
  Lines         1223     1223                 
  Branches       489        0      -489       
============================================  
+ Hits           868     1166      +298       
  Misses          57       57                 
+ Partials       298        0      -298       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/ublas/pull/91?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [.../boost/numeric/ublas/tensor/fixed\_rank\_strides.hpp](https://codecov.io/gh/boostorg/ublas/pull/91/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9maXhlZF9yYW5rX3N0cmlkZXMuaHBw) | `95.23% <0.00%> (+7.14%)` | :arrow_up: |  
| [...ude/boost/numeric/ublas/tensor/dynamic\_strides.hpp](https://codecov.io/gh/boostorg/ublas/pull/91/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9keW5hbWljX3N0cmlkZXMuaHBw) | `95.34% <0.00%> (+9.30%)` | :arrow_up: |  
| [...ost/numeric/ublas/tensor/expression\_evaluation.hpp](https://codecov.io/gh/boostorg/ublas/pull/91/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9leHByZXNzaW9uX2V2YWx1YXRpb24uaHBw) | `96.07% <0.00%> (+9.80%)` | :arrow_up: |  
| [include/boost/numeric/ublas/tensor/tensor.hpp](https://codecov.io/gh/boostorg/ublas/pull/91/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci90ZW5zb3IuaHBw) | `100.00% <0.00%> (+9.82%)` | :arrow_up: |  
| [.../boost/numeric/ublas/tensor/fixed\_rank\_extents.hpp](https://codecov.io/gh/boostorg/ublas/pull/91/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9maXhlZF9yYW5rX2V4dGVudHMuaHBw) | `94.28% <0.00%> (+11.42%)` | :arrow_up: |  
| [...ude/boost/numeric/ublas/tensor/dynamic\_extents.hpp](https://codecov.io/gh/boostorg/ublas/pull/91/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9keW5hbWljX2V4dGVudHMuaHBw) | `97.67% <0.00%> (+13.95%)` | :arrow_up: |  
| [include/boost/numeric/ublas/tensor/strides.hpp](https://codecov.io/gh/boostorg/ublas/pull/91/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9zdHJpZGVzLmhwcA==) | `100.00% <0.00%> (+16.66%)` | :arrow_up: |  
| [include/boost/numeric/ublas/tensor/expression.hpp](https://codecov.io/gh/boostorg/ublas/pull/91/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9leHByZXNzaW9uLmhwcA==) | `100.00% <0.00%> (+20.00%)` | :arrow_up: |  
| [...lude/boost/numeric/ublas/tensor/multiplication.hpp](https://codecov.io/gh/boostorg/ublas/pull/91/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9tdWx0aXBsaWNhdGlvbi5ocHA=) | `86.66% <0.00%> (+20.83%)` | :arrow_up: |  
| [...oost/numeric/ublas/tensor/operators\_arithmetic.hpp](https://codecov.io/gh/boostorg/ublas/pull/91/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9vcGVyYXRvcnNfYXJpdGhtZXRpYy5ocHA=) | `100.00% <0.00%> (+25.45%)` | :arrow_up: |  
| ... and [8 more](https://codecov.io/gh/boostorg/ublas/pull/91/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/91?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/91?src=pr&el=footer). Last update [0584e2a...eb1cdfd](https://codecov.io/gh/boostorg/ublas/pull/91?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: coder3101  
> Created_at: 2020-07-11 17:16:10 UTC  
> Url: https://github.com/boostorg/ublas/pull/91#issuecomment-657097792  

@bassoy and @amitsingh19975 Please approve and merge this PR

---

## Review 3 [Approved]

> Username: amitsingh19975  
> Created_at: 2020-07-12 11:52:34 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/91#pullrequestreview-446871551  

This will improve the coverage which is reduced due to exceptions.

---

## Review 4 [Approved]

> Username: bassoy  
> Created_at: 2020-07-12 13:20:57 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/91#pullrequestreview-446878040  

I am approving the changes. In future we might want to reenable branch coverage.

---
