# #1792 Pr1791 travis fix [Closed]

> Username: madmongo1  
> Created at: 2019-12-24 16:52:55 UTC  
> Updated at: 2019-12-24 17:50:30 UTC  
> Closed at: 2019-12-24 17:50:30 UTC  
> Url: https://github.com/boostorg/beast/pull/1792  

Extends PR1791 with a fix to prevent compiler error under clang due to narrowing of chars.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2019-12-24 16:57:57 UTC  
> Url: https://github.com/boostorg/beast/pull/1792#issuecomment-568778561  

This needs to be squashed down to either 1 or 2 commits, with the original author preserved. If you want authorship credit then the 2nd commit should have all of your fixes.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2019-12-24 16:58:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1792#issuecomment-568778643  

Suggested commit messages:  
> file_win32 supports UTF-8 paths  
> file_stdio supports unicode paths

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2019-12-24 16:59:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1792#issuecomment-568778685  

Actually there are 2 or 3 commits here, the file_win32 and file_stdio should be individual commits, and then if you want authorship, all of your fixes in a 3rd commit.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2019-12-24 17:23:36 UTC  
> Url: https://github.com/boostorg/beast/pull/1792#issuecomment-568781638  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1792?src=pr&el=h1) Report  
> Merging [#1792](https://codecov.io/gh/boostorg/beast/pull/1792?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/45cb52f65150c33319afcce6893b36c7de937e7c?src=pr&el=desc) will **increase** coverage by `0.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1792/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1792?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1792      +/-   ##  
===========================================  
+ Coverage    95.15%   95.17%   +0.01%       
===========================================  
  Files          156      156                
  Lines        11949    11949                
===========================================  
+ Hits         11370    11372       +2       
+ Misses         579      577       -2  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1792?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/file\_stdio.ipp](https://codecov.io/gh/boostorg/beast/pull/1792/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmlsZV9zdGRpby5pcHA=) | `84.29% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1792/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.6% <0%> (+0.1%)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1792/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.57% <0%> (+0.19%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1792?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1792?src=pr&el=footer). Last update [45cb52f...04581e1](https://codecov.io/gh/boostorg/beast/pull/1792?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
