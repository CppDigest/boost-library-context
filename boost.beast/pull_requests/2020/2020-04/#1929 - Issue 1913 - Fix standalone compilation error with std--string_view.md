# #1929 Issue 1913 - Fix standalone compilation error with std::string_view [Closed]

> Username: madmongo1  
> Created at: 2020-04-30 15:19:15 UTC  
> Updated at: 2020-05-01 05:16:32 UTC  
> Closed at: 2020-05-01 05:16:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1929  

* Fix standalone compilation error with std::string_view   
* Test standalone compilation with std::string_view

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-04-30 15:20:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1929#pullrequestreview-403624815  

📁 test/beast/http/field_compiles.cpp

```diff
   2 |+ // check that http/field.hpp compiles unaided
   3 |+ //
   4 |+ #include <boost/beast/http/field.hpp>
```

> Username: vinniefalco  
> Created_at: 2020-04-30 15:20:07 UTC  
> Updated_at: 2020-04-30 15:56:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1929#discussion_r418090398  

`field.cpp` should accomplish this, no?

> Username: vinniefalco  
> Created_at: 2020-04-30 15:26:48 UTC  
> Updated_at: 2020-04-30 15:56:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1929#discussion_r418095268  

See:  
https://github.com/boostorg/beast/blob/83fc0018a89863c6c2aa3cf932313664f8651e9b/test/beast/http/field.cpp#L11

> Username: madmongo1  
> Created_at: 2020-04-30 15:32:22 UTC  
> Updated_at: 2020-04-30 15:56:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1929#discussion_r418099227  

no, because it pulls in the test stuff, which pulls in iostream.  
  
I obviously tried this first.


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-04-30 15:22:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1929#pullrequestreview-403626567  

📁 include/boost/beast/http/impl/field.ipp

```diff
  18 |+ #if BOOST_BEAST_SEPARATE_COMPILATION
  19 |+ #include <iostream>
  20 |+ #endif
```

> Username: vinniefalco  
> Created_at: 2020-04-30 15:22:01 UTC  
> Updated_at: 2020-04-30 15:56:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1929#discussion_r418091818  

can we please reorder these to put all std includes last?

> Username: madmongo1  
> Created_at: 2020-04-30 15:33:08 UTC  
> Updated_at: 2020-04-30 15:56:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1929#discussion_r418099713  

This would be touching more of the file than is relevant to the fix, but sure.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-04-30 15:51:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1929#pullrequestreview-403653472  

📁 test/beast/http/field_compiles.cpp

```diff
   2 |+ // check that http/field.hpp compiles unaided
   3 |+ //
   4 |+ #include <boost/beast/http/field.hpp>
```

> Username: vinniefalco  
> Created_at: 2020-04-30 15:51:53 UTC  
> Updated_at: 2020-04-30 15:56:03 UTC  
> Url: https://github.com/boostorg/beast/pull/1929#discussion_r418112718  

The file is missing the copyright notice and license. You can look at field.cpp and copy that


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2020-04-30 15:59:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1929#pullrequestreview-403658510  

📁 include/boost/beast/http/impl/field.ipp

```diff
  17 |- #include <boost/assert.hpp>
  18 |+ #if BOOST_BEAST_SEPARATE_COMPILATION
  19 |+ #include <iostream>
```

> Username: pdimov  
> Created_at: 2020-04-30 15:58:47 UTC  
> Updated_at: 2020-04-30 15:59:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1929#discussion_r418117495  

`<ostream>`, not `<iostream>`.  
  
Not sure about the ifdef; the header-only config will fail in the same way without the include.

> Username: vinniefalco  
> Created_at: 2020-04-30 16:07:32 UTC  
> Updated_at: 2020-04-30 16:07:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1929#discussion_r418123349  

The intent is that the user must include `<ostream>` if they want to use `operator<<` on `http::field`. For header-only, the user is the user of Beast. For the compiled lib, the user is the TU used to build the lib.

> Username: pdimov  
> Created_at: 2020-04-30 16:11:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1929#discussion_r418125819  

As I said, I don't see why this code won't fail in the same way then, when the user doesn't use op<<.

> Username: vinniefalco  
> Created_at: 2020-04-30 16:39:05 UTC  
> Updated_at: 2020-04-30 16:39:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1929#discussion_r418143379  

Yes I see what you mean. `<ostream>` should be included unconditionally.


---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2020-05-01 01:23:28 UTC  
> Url: https://github.com/boostorg/beast/pull/1929#issuecomment-622203664  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1929?src=pr&el=h1) Report  
> Merging [#1929](https://codecov.io/gh/boostorg/beast/pull/1929?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/f5064e0c460664fa34fdad44e2379a2309d39685&el=desc) will **decrease** coverage by `0.03%`.  
> The diff coverage is `50.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1929/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1929?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1929      +/-   ##  
===========================================  
- Coverage    95.13%   95.10%   -0.04%       
===========================================  
  Files          156      156                
  Lines        12032    12034       +2       
===========================================  
- Hits         11447    11445       -2       
- Misses         585      589       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1929?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/field.ipp](https://codecov.io/gh/boostorg/beast/pull/1929/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGQuaXBw) | `95.29% <0.00%> (-2.30%)` | :arrow_down: |  
| [include/boost/beast/core/detect\_ssl.hpp](https://codecov.io/gh/boostorg/beast/pull/1929/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGVjdF9zc2wuaHBw) | `95.08% <100.00%> (ø)` | |  
| [...ude/boost/beast/core/impl/buffered\_read\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/1929/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyZWRfcmVhZF9zdHJlYW0uaHBw) | `92.30% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/1929/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0.00%> (-0.90%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1929/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.37% <0.00%> (-0.20%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1929?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1929?src=pr&el=footer). Last update [83fc001...6f34a15](https://codecov.io/gh/boostorg/beast/pull/1929?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
