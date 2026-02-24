# #1973 Deprecate `string_param` (API Change): [Closed]

> Username: madmongo1  
> Created at: 2020-06-04 17:20:35 UTC  
> Updated at: 2020-06-12 19:20:32 UTC  
> Closed at: 2020-06-12 19:20:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1973  

API Changes:  
  
`string_param`, which was previously the argument type when setting field values  
has been replaced by `string_view`. Because of this, it is no longer possible to  
set message field values directly as integrals.  
  
Users are requied to convert numeric arguments to a string type prior to calling  
`fields::set` et. al.  
  
Beast provides the non-allocating `to_static_string()` function for this purpose.  
  
To set Content-Length field manually, call `message::content_length`.  
  
fixes #1956

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-06-04 17:26:20 UTC  
> Url: https://github.com/boostorg/beast/pull/1973#issuecomment-638995405  

An automated preview of the documentation is available at [http://1973.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](http://1973.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-06-04 19:08:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1973#issuecomment-639060032  

An automated preview of the documentation is available at [http://1973.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](http://1973.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2020-06-05 00:37:22 UTC  
> Url: https://github.com/boostorg/beast/pull/1973#issuecomment-639191334  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1973?src=pr&el=h1) Report  
> Merging [#1973](https://codecov.io/gh/boostorg/beast/pull/1973?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/f25c904d149a5d0a1c54a5f3d8c9045bf27a4fda&el=desc) will **decrease** coverage by `0.02%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1973/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1973?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1973      +/-   ##  
===========================================  
- Coverage    95.13%   95.10%   -0.03%       
===========================================  
  Files          155      152       -3       
  Lines        12057    11960      -97       
===========================================  
- Hits         11470    11375      -95       
+ Misses         587      585       -2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1973?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/1973/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/http/impl/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/1973/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmhwcA==) | `97.78% <100.00%> (ø)` | |  
| [include/boost/beast/core/impl/string\_param.hpp](https://codecov.io/gh/boostorg/beast/pull/1973/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvc3RyaW5nX3BhcmFtLmhwcA==) | | |  
| [include/boost/beast/core/detail/static\_ostream.hpp](https://codecov.io/gh/boostorg/beast/pull/1973/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9zdGF0aWNfb3N0cmVhbS5ocHA=) | | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1973?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1973?src=pr&el=footer). Last update [f25c904...4d5d10b](https://codecov.io/gh/boostorg/beast/pull/1973?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
