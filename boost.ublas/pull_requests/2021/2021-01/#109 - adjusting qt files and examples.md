# #109 adjusting qt files and examples. [Merged]

> Username: bassoy  
> Created at: 2021-01-10 18:39:23 UTC  
> Updated at: 2021-05-24 18:52:38 UTC  
> Merged at: 2021-01-12 17:22:17 UTC  
> Closed at: 2021-01-12 17:22:17 UTC  
> Url: https://github.com/boostorg/ublas/pull/109  

- Adjusted outdated Qtcreator files  
- Renamed examples  
- Updated test Jamfile with one executable

---

## Review 1 [Commented]

> Username: github-actions[bot]  
> Created_at: 2021-01-10 18:44:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/109#pullrequestreview-564889893  

Reports by clang tidy

📁 examples/tensor/static_tensor.cpp

```diff
  71 |+ 
  72 |+ 
  73 |+ int main()
```

> Username: github-actions[bot]  
> Created_at: 2021-01-10 18:44:10 UTC  
> Updated_at: 2021-01-10 19:13:12 UTC  
> Url: https://github.com/boostorg/ublas/pull/109#discussion_r554606625  

redefinition of `main` [clang-diagnostic-error]  
```cpp  
int main()  
    ^  
examples/tensor/static_tensor.cpp:20:5: note: previous definition is here  
int main() {  
    ^  
```  
Reported as error by clang-tidy.


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-01-10 18:48:17 UTC  
> Updated_at: 2021-01-10 19:23:42 UTC  
> Url: https://github.com/boostorg/ublas/pull/109#issuecomment-757524298  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/109?src=pr&el=h1) Report  
> Merging [#109](https://codecov.io/gh/boostorg/ublas/pull/109?src=pr&el=desc) (5801f5c) into [develop](https://codecov.io/gh/boostorg/ublas/commit/a81712c3195183139fc7923dc32031dab2859ec9?el=desc) (a81712c) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/109/graphs/tree.svg?width=650&height=150&src=pr&token=CShoBArRWq)](https://codecov.io/gh/boostorg/ublas/pull/109?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #109   +/-   ##  
========================================  
  Coverage    94.54%   94.54%             
========================================  
  Files           19       19             
  Lines         1192     1192             
========================================  
  Hits          1127     1127             
  Misses          65       65             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/ublas/pull/109?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/numeric/ublas/tensor/functions.hpp](https://codecov.io/gh/boostorg/ublas/pull/109/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL3VibGFzL3RlbnNvci9mdW5jdGlvbnMuaHBw) | `94.94% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/109?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/109?src=pr&el=footer). Last update [a81712c...3181e31](https://codecov.io/gh/boostorg/ublas/pull/109?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: github-actions[bot]  
> Created_at: 2021-01-10 19:22:59 UTC  
> Url: https://github.com/boostorg/ublas/pull/109#issuecomment-757529375  

This Pull request Passed all of clang-tidy tests. :+1:

---

## Review 4 [Approved]

> Username: amitsingh19975  
> Created_at: 2021-01-12 08:20:13 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/109#pullrequestreview-566027256  

It looks good.

---
