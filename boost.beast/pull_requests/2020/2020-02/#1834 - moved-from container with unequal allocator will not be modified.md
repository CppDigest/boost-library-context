# #1834 moved-from container with unequal allocator will not be modified [Closed]

> Username: madmongo1  
> Created at: 2020-02-04 09:47:43 UTC  
> Updated at: 2020-02-05 14:40:52 UTC  
> Closed at: 2020-02-05 14:40:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1834  

Currently proposed as 3 commits for discussion and analysis.  
  
Will squash before final review.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-04 09:59:30 UTC  
> Updated_at: 2020-02-04 16:04:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1834#issuecomment-581830841  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1834?src=pr&el=h1) Report  
> Merging [#1834](https://codecov.io/gh/boostorg/beast/pull/1834?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c9d5049307925bccaf2f5e57c127db1a1c78e178?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1834/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1834?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1834      +/-   ##  
===========================================  
- Coverage    95.19%   95.18%   -0.01%       
===========================================  
  Files          156      156                
  Lines        11956    11946      -10       
===========================================  
- Hits         11381    11371      -10       
  Misses         575      575  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1834?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1834/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvbXVsdGlfYnVmZmVyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/flat\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/1834/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmxhdF9idWZmZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/1834/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmhwcA==) | `97.78% <ø> (+0.21%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/1834/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0%> (-0.9%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1834?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1834?src=pr&el=footer). Last update [c9d5049...f5b0313](https://codecov.io/gh/boostorg/beast/pull/1834?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-02-04 13:58:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1834#issuecomment-581921494  

not much to talk about here, it looks correct

---

## Comment 3

> Username: madmongo1  
> Created_at: 2020-02-04 15:09:20 UTC  
> Url: https://github.com/boostorg/beast/pull/1834#issuecomment-581954582  

OK, will squash and force-push

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-04 15:20:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1834#pullrequestreview-353060924  

📁 CHANGELOG.md

```diff
   1 |+ Version XXX:
```

> Username: vinniefalco  
> Created_at: 2020-02-04 15:20:40 UTC  
> Url: https://github.com/boostorg/beast/pull/1834#discussion_r374735434  

why not just put 285 here

> Username: madmongo1  
> Created_at: 2020-02-04 17:29:19 UTC  
> Updated_at: 2020-02-04 17:29:20 UTC  
> Url: https://github.com/boostorg/beast/pull/1834#discussion_r374815208  

in case it doesn't make it into 285

> Username: vinniefalco  
> Created_at: 2020-02-04 17:41:46 UTC  
> Updated_at: 2020-02-04 17:41:47 UTC  
> Url: https://github.com/boostorg/beast/pull/1834#discussion_r374821440  

XXX is what you put on a barrel full of dynamite


---
