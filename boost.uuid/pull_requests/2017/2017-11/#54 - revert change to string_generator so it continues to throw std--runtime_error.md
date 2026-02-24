# #54 revert change to string_generator so it continues to throw std::runtime_error [Merged]

> Username: jeking3  
> Created at: 2017-11-10 22:51:21 UTC  
> Updated at: 2017-11-11 20:08:03 UTC  
> Merged at: 2017-11-11 19:15:36 UTC  
> Closed at: 2017-11-11 19:15:36 UTC  
> Url: https://github.com/boostorg/uuid/pull/54  

… like it always had before; per request from @Lastique.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-11-11 00:32:22 UTC  
> Url: https://github.com/boostorg/uuid/pull/54#issuecomment-343622771  

I think you also need to change the docs (uuid.html:504).

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-11-11 05:42:28 UTC  
> Url: https://github.com/boostorg/uuid/pull/54#issuecomment-343642731  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/54?src=pr&el=h1) Report  
> Merging [#54](https://codecov.io/gh/boostorg/uuid/pull/54?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/uuid/commit/5a48cab9af5b522c6f4851b7312761493e2e4c78?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/54/graphs/tree.svg?token=6falIr5npV&width=650&height=150&src=pr)](https://codecov.io/gh/boostorg/uuid/pull/54?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop     #54      +/-   ##  
==========================================  
- Coverage    97.41%   97.4%   -0.01%       
==========================================  
  Files           11      11                
  Lines          619     617       -2       
==========================================  
- Hits           603     601       -2       
  Misses          16      16  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/uuid/pull/54?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/uuid/string\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/54?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3N0cmluZ19nZW5lcmF0b3IuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/54?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/54?src=pr&el=footer). Last update [5a48cab...8a81273](https://codecov.io/gh/boostorg/uuid/pull/54?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-11-11 20:08:03 UTC  
> Url: https://github.com/boostorg/uuid/pull/54#issuecomment-343690753  

Thanks.

---
