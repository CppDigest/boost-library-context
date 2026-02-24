# #613 Version 74 [Merged]

> Username: vinniefalco  
> Created at: 2017-07-06 20:45:29 UTC  
> Updated at: 2017-07-07 22:16:05 UTC  
> Merged at: 2017-07-07 20:55:49 UTC  
> Closed at: 2017-07-07 20:55:49 UTC  
> Url: https://github.com/boostorg/beast/pull/613  

* Add file_stdio and File concept  
* Add file_win32  
* Add file_body  
* Remove common/file_body.hpp  
* Using SSE4.2 intrinsics in basic_parser if available  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v74/

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-06 21:27:25 UTC  
> Updated_at: 2017-07-07 22:16:05 UTC  
> Url: https://github.com/boostorg/beast/pull/613#issuecomment-313524265  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=h1) Report  
> Merging [#613](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/89c416cde64de3265299ced216a6eef9428c2a51?src=pr&el=desc) will **decrease** coverage by `0.39%`.  
> The diff coverage is `78.44%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/613/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop     #613     +/-   ##  
==========================================  
- Coverage    93.84%   93.45%   -0.4%       
==========================================  
  Files           94      100      +6       
  Lines         7476     7698    +222       
==========================================  
+ Hits          7016     7194    +178       
- Misses         460      504     +44  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/file\_stdio.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZpbGVfc3RkaW8uaHBw) | `100% <100%> (ø)` | |  
| [include/beast/core/file.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZpbGUuaHBw) | `100% <100%> (ø)` | |  
| [include/beast/core/file\_posix.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZpbGVfcG9zaXguaHBw) | `100% <100%> (ø)` | |  
| [include/beast/http/file\_body.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ZpbGVfYm9keS5ocHA=) | `82.05% <77.41%> (ø)` | |  
| [include/beast/core/impl/file\_stdio.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvZmlsZV9zdGRpby5pcHA=) | `78.04% <78.04%> (ø)` | |  
| [include/beast/core/impl/file\_posix.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ltcGwvZmlsZV9wb3NpeC5pcHA=) | `78.21% <78.21%> (ø)` | |  
| [include/beast/http/parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `79.48% <0%> (-1.01%)` | :arrow_down: |  
| [include/beast/http/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `90.5% <0%> (-0.1%)` | :arrow_down: |  
| ... and [5 more](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=footer). Last update [89c416c...6f88f01](https://codecov.io/gh/vinniefalco/Beast/pull/613?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
