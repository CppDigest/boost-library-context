# #1793 Pr1791 narrowing fix [Closed]

> Username: madmongo1  
> Created at: 2019-12-24 17:53:04 UTC  
> Updated at: 2019-12-29 19:20:48 UTC  
> Closed at: 2019-12-29 19:20:48 UTC  
> Url: https://github.com/boostorg/beast/pull/1793  

Ammends PR1791 By Mike Fischer:  
  
Original Comment:  
==============  
This brings file_win32 in sync with the documentation.  
Previously, the path passed to open worked if encoded in the system  
codepage (which is almost never UTF-8).  
Now, the path must be encoded as UTF-8, as stated in the  
documentation.  
  
Adapt file tests so that for file_win32 all paths include a unicorn  
character.  
  
Note that file_stdio on Windows is still broken as before.  
  
Partially fixes #793  
  
Further Edits:  
==========  
* Fix narrowing conversion compile error only evidenced in clang build  
* Amend Change Log and Version

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-12-24 18:06:18 UTC  
> Updated_at: 2019-12-24 21:24:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1793#issuecomment-568786415  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1793?src=pr&el=h1) Report  
> Merging [#1793](https://codecov.io/gh/boostorg/beast/pull/1793?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/45cb52f65150c33319afcce6893b36c7de937e7c?src=pr&el=desc) will **increase** coverage by `0.02%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1793/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1793?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1793      +/-   ##  
===========================================  
+ Coverage    95.15%   95.17%   +0.02%       
===========================================  
  Files          156      156                
  Lines        11949    11949                
===========================================  
+ Hits         11370    11373       +3       
+ Misses         579      576       -3  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1793?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/file\_stdio.ipp](https://codecov.io/gh/boostorg/beast/pull/1793/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmlsZV9zdGRpby5pcHA=) | `84.29% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/deflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1793/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9kZWZsYXRlX3N0cmVhbS5pcHA=) | `88.6% <0%> (+0.1%)` | :arrow_up: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1793/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.57% <0%> (+0.19%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/1793/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `97.32% <0%> (+0.89%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1793?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1793?src=pr&el=footer). Last update [45cb52f...2603610](https://codecov.io/gh/boostorg/beast/pull/1793?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2019-12-24 18:48:47 UTC  
> Url: https://github.com/boostorg/beast/pull/1793#issuecomment-568790655  

Suggested revision:  
```  
file_win32 supports UTF-8 paths:  
  
fix #793, close #1791, close #1793  
  
This brings file_win32 in sync with the documentation.  
Previously, the path passed to open worked if encoded in the system  
codepage (which is almost never UTF-8).  
Now, the path must be encoded as UTF-8, as stated in the  
documentation.  
  
Adapt file tests so that for file_win32 all paths include a unicorn  
character.  
```

---

## Review 3 [Commented]

> Username: Flamefire  
> Created_at: 2019-12-25 11:06:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1793#pullrequestreview-336350806  

📁 CHANGELOG.md

```diff
   2 |+ 
   3 |+ * file_win32 supports UTF-8 paths
   4 |+ * file_stdio supports unicode paths
```

> Username: Flamefire  
> Created_at: 2019-12-25 11:06:29 UTC  
> Updated_at: 2019-12-25 11:06:30 UTC  
> Url: https://github.com/boostorg/beast/pull/1793#discussion_r361299999  

More correct: `file_stdio supports UTF-8 paths on MSVC` :/

> Username: vinniefalco  
> Created_at: 2019-12-25 16:15:18 UTC  
> Updated_at: 2019-12-25 16:15:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1793#discussion_r361325133  

I guess that is more accurate..are you saying that other toolchains on windows don't use the dinkum stdlib or whatever it is that msvc uses? I am ignorant about such things (I pretty much stay in visual studio always).

> Username: Flamefire  
> Created_at: 2019-12-27 11:12:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1793#discussion_r361641591  

No, it really is the MSVC compiler only due to usage of `BOOST_MSVC`:   
  
> Defined if the compiler is really Microsoft Visual C++, as opposed to one of the many other compilers that also define _MSC_VER. Has the same value as _MSC_VER.   
  
from https://www.boost.org/doc/libs/master/libs/config/doc/html/boost_config/boost_macro_reference.html  
  
In #1791 I proposed to use `_wfopen` if on windows but not on MSVC and adjust the checks accordingly. Then it will either fail to compile (unlikely as AFAIK all windows compiler libs have `_wfopen`) or work as advertised which is a desirable outcome


---
