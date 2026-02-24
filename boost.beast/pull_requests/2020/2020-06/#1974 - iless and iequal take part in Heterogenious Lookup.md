# #1974 iless and iequal take part in Heterogenious Lookup [Closed]

> Username: madmongo1  
> Created at: 2020-06-05 13:38:03 UTC  
> Updated at: 2020-06-12 19:20:33 UTC  
> Closed at: 2020-06-12 19:20:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1974  

Fixes #1949

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-05 13:40:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1974#pullrequestreview-425308143  

📁 include/boost/beast/core/string.hpp

```diff
  36 |+ 
  37 |+     As of c++14 containers using this class as the Compare type will take part
  38 |+     in heterogenious lookup if the search term is implicitly convertible to string_view.
```

> Username: vinniefalco  
> Created_at: 2020-06-05 13:40:37 UTC  
> Updated_at: 2020-06-05 13:46:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1974#discussion_r435928063  

Spelling: heterogeneous  
Capitalization: C++14  
Punctuation: comma after C++14  
"Compare" should be in back-quotes and string_view should be a `@ref`.

> Username: madmongo1  
> Created_at: 2020-06-05 13:41:50 UTC  
> Updated_at: 2020-06-05 13:46:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1974#discussion_r435928823  

For the error in spelling I am contrite. Unforgivable.

> Username: vinniefalco  
> Created_at: 2020-06-05 13:42:55 UTC  
> Updated_at: 2020-06-05 13:46:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1974#discussion_r435929487  

Probably shouldn't capitalize heterogeneous

> Username: vinniefalco  
> Created_at: 2020-06-05 13:43:16 UTC  
> Updated_at: 2020-06-05 13:46:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1974#discussion_r435929683  

I would say "key" instead of "search term".

> Username: madmongo1  
> Created_at: 2020-06-05 13:44:02 UTC  
> Updated_at: 2020-06-05 13:46:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1974#discussion_r435930134  

I see you are a proponent of the Oxford comma. The subject of bitter debates between literary experts in the UK :-)

> Username: vinniefalco  
> Created_at: 2020-06-05 14:07:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1974#discussion_r435946631  

I could go either way on the comma


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-06-05 13:50:29 UTC  
> Updated_at: 2020-06-05 14:17:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1974#issuecomment-639491956  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1974?src=pr&el=h1) Report  
> Merging [#1974](https://codecov.io/gh/boostorg/beast/pull/1974?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/f25c904d149a5d0a1c54a5f3d8c9045bf27a4fda&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1974/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1974?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1974   +/-   ##  
========================================  
  Coverage    95.13%   95.13%             
========================================  
  Files          155      155             
  Lines        12057    12057             
========================================  
  Hits         11470    11470             
  Misses         587      587             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1974?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1974?src=pr&el=footer). Last update [f25c904...c8e02b2](https://codecov.io/gh/boostorg/beast/pull/1974?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
