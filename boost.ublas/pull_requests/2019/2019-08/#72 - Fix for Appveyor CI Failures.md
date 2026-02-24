# #72 Fix for Appveyor CI Failures [Merged]

> Username: coder3101  
> Created at: 2019-08-18 20:47:03 UTC  
> Updated at: 2020-03-14 13:59:20 UTC  
> Merged at: 2020-03-14 13:59:20 UTC  
> Closed at: 2020-03-14 13:59:20 UTC  
> Url: https://github.com/boostorg/ublas/pull/72  

Recently due to some changes to vcpkg the CI was failing to work with VS2017 using cached vcpkg. This is however fixed if we update the vcpkg. To prevent this from happening in future I am adding the upgrade to vcpkg as a pre-step for building our code.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-08-18 21:51:30 UTC  
> Url: https://github.com/boostorg/ublas/pull/72#issuecomment-522358925  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/72?src=pr&el=h1) Report  
> Merging [#72](https://codecov.io/gh/boostorg/ublas/pull/72?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/ublas/commit/cf72d035f4f54c1fca3fa8882007a338c03b594b?src=pr&el=desc) will **decrease** coverage by `2.31%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/72/graphs/tree.svg?width=650&token=CShoBArRWq&height=150&src=pr)](https://codecov.io/gh/boostorg/ublas/pull/72?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop     #72      +/-   ##  
==========================================  
- Coverage    67.42%   65.1%   -2.32%       
==========================================  
  Files           61      61                
  Lines         9233    9233                
  Branches      3000    2996       -4       
==========================================  
- Hits          6225    6011     -214       
- Misses         872     894      +22       
- Partials      2136    2328     +192  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/ublas/pull/72?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/numeric/ublas/opencl/misc.hpp](https://codecov.io/gh/boostorg/ublas/pull/72/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL29wZW5jbC9taXNjLmhwcA==) | `25% <0%> (-50%)` | :arrow_down: |  
| [test/test2.cpp](https://codecov.io/gh/boostorg/ublas/pull/72/diff?src=pr&el=tree#diff-dGVzdC90ZXN0Mi5jcHA=) | `62.5% <0%> (-37.5%)` | :arrow_down: |  
| [include/boost/numeric/ublas/opencl/prod.hpp](https://codecov.io/gh/boostorg/ublas/pull/72/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL29wZW5jbC9wcm9kLmhwcA==) | `29.55% <0%> (-20.76%)` | :arrow_down: |  
| [include/boost/numeric/ublas/opencl/transpose.hpp](https://codecov.io/gh/boostorg/ublas/pull/72/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL29wZW5jbC90cmFuc3Bvc2UuaHBw) | `46.87% <0%> (-18.75%)` | :arrow_down: |  
| [include/boost/numeric/ublas/opencl/elementwise.hpp](https://codecov.io/gh/boostorg/ublas/pull/72/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL29wZW5jbC9lbGVtZW50d2lzZS5ocHA=) | `42.85% <0%> (-10.21%)` | :arrow_down: |  
| [include/boost/numeric/ublas/tensor/functions.hpp](https://codecov.io/gh/boostorg/ublas/pull/72/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9mdW5jdGlvbnMuaHBw) | `38.41% <0%> (-4.88%)` | :arrow_down: |  
| [include/boost/numeric/ublas/opencl/vector.hpp](https://codecov.io/gh/boostorg/ublas/pull/72/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL29wZW5jbC92ZWN0b3IuaHBw) | `57.14% <0%> (-4.77%)` | :arrow_down: |  
| [include/boost/numeric/ublas/opencl/matrix.hpp](https://codecov.io/gh/boostorg/ublas/pull/72/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL29wZW5jbC9tYXRyaXguaHBw) | `63.33% <0%> (-3.34%)` | :arrow_down: |  
| [include/boost/numeric/ublas/vector\_sparse.hpp](https://codecov.io/gh/boostorg/ublas/pull/72/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3ZlY3Rvcl9zcGFyc2UuaHBw) | `74.29% <0%> (-3.2%)` | :arrow_down: |  
| [include/boost/numeric/ublas/matrix\_sparse.hpp](https://codecov.io/gh/boostorg/ublas/pull/72/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL21hdHJpeF9zcGFyc2UuaHBw) | `60.69% <0%> (-3.12%)` | :arrow_down: |  
| ... and [15 more](https://codecov.io/gh/boostorg/ublas/pull/72/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/72?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/72?src=pr&el=footer). Last update [cf72d03...cdf219c](https://codecov.io/gh/boostorg/ublas/pull/72?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
