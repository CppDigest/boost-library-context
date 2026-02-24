# #422 Version 49 [Closed]

> Username: vinniefalco  
> Created at: 2017-06-05 04:17:11 UTC  
> Updated at: 2017-06-08 01:19:46 UTC  
> Closed at: 2017-06-05 23:08:56 UTC  
> Url: https://github.com/boostorg/beast/pull/422  

This includes version 48  
  
* Use <iosfwd> instead of <ostream>  
  
HTTP:  
  
* Add HEAD request example  
  
API Changes:  
  
* Refactor method and verb  
* Canonicalize string_view parameter types  
* Tidy up empty_body writer error  
* Refactor header status, reason, and target  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v49

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-06-05 04:43:43 UTC  
> Updated_at: 2017-06-05 12:39:56 UTC  
> Url: https://github.com/boostorg/beast/pull/422#issuecomment-306101982  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=h1) Report  
> Merging [#422](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=desc) into [master](https://codecov.io/gh/vinniefalco/Beast/commit/458fa7e4e239e1c9e41066e61da2ae8f71d26220?src=pr&el=desc) will **decrease** coverage by `0.09%`.  
> The diff coverage is `93.08%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/422/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&width=650&height=150)](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff           @@  
##           master   #422     +/-   ##  
=======================================  
- Coverage    94.1%    94%   -0.1%       
=======================================  
  Files          94     93      -1       
  Lines        6678   6676      -2       
=======================================  
- Hits         6284   6276      -8       
- Misses        394    400      +6  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/core/detail/ostream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2RldGFpbC9vc3RyZWFtLmhwcA==) | `93.02% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/verb.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3ZlcmIuaHBw) | `100% <ø> (ø)` | |  
| [include/beast/http/impl/fields.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/impl/handshake.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaXBw) | `96.59% <ø> (+0.16%)` | :arrow_up: |  
| [include/beast/http/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/rfc7230.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3JmYzcyMzAuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/ostream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL29zdHJlYW0uaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/core/flat\_buffer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/websocket/stream.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `99.31% <ø> (-0.01%)` | :arrow_down: |  
| ... and [38 more](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=footer). Last update [458fa7e...97bda6f](https://codecov.io/gh/vinniefalco/Beast/pull/422?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Changes requested]

> Username: nbougalis  
> Created_at: 2017-06-05 21:54:13 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/beast/pull/422#pullrequestreview-42144236  

Looks awesome. I did an exhaustive reading of the documentation and noted any nits I found.  
  
Godspeed on the Boost review, @vinniefalco!

📁 doc/1_overview.qbk

```diff
 123 | [@https://www.ripple.com Ripple Labs]
 124 | for supporting its early development. Also thanks to
```

> Username: nbougalis  
> Created_at: 2017-06-05 20:27:10 UTC  
> Updated_at: 2017-06-05 21:54:13 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120201006  

This paragraph does not read right. Recommend:  
  
>Beast would not be possible without the support of [@https://www.ripple.com Ripple] during the library's early development, or the ideas, time and patience contributed by [@https://github.com/JoelKatz David Schwartz], [@https://github.com/ximinez Edward Hennis], [@https://github.com/howardhinnant Howard Hinnant], [@https://github.com/miguelportilla Miguel Portilla], [@https://github.com/seelabs Scott Determan], [@https://github.com/scottschurr] and [https://github.com/nbougalis Nik Bougalis]. Many thanks to Agustín Bergé, [@http://www.boost.org/users/people/glen_fernandes.html Glen Fernandes] and Peter Dimov for their considerable help.

> Username: vinniefalco  
> Created_at: 2017-06-05 22:59:42 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120230584  

willfix

---

📁 doc/1_overview.qbk

```diff
  19 |+     example programs shows how a client or server might be built.
  20 |+ ]
  21 |+ 
```

> Username: nbougalis  
> Created_at: 2017-06-05 20:33:35 UTC  
> Updated_at: 2017-06-05 21:54:13 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120202422  

Slightly below (around line 58) you include this note:  
  
>[note Tested compilers: msvc-14+, gcc 4.8+, clang 3.6+]  
  
I suspect you want to write **Supported** instead of **Tested**. Rationale: you haven't _tested_ every gcc from 4.8 to 7.whatever, or every clang, but you do support them.

> Username: nbougalis  
> Created_at: 2017-06-05 20:35:03 UTC  
> Updated_at: 2017-06-05 21:54:13 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120202795  

Line 68, typo: "noticable" should be "noticeable"

> Username: vinniefalco  
> Created_at: 2017-06-05 22:53:57 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120229827  

willfix

> Username: vinniefalco  
> Created_at: 2017-06-05 22:55:00 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120229952  

willfix


📁 doc/3_2_streams.qbk

```diff
   8 |+ [section:streams Using Streams]
   9 |+ 
  10 |+ A __Stream__ is communication channel where data expressed as octet
```

> Username: nbougalis  
> Created_at: 2017-06-05 20:40:40 UTC  
> Updated_at: 2017-06-05 21:54:13 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120204087  

A Stream is **a** communication channel


📁 doc/3_3_buffers.qbk

```diff
  83 |+ No memory allocations are used in the transformations; instead, they
  84 |+ create lightweight iterators over the existing, unmodified memory
  85 |+ buffers. The lifetime of the transformed buffers is retained by the
```

> Username: nbougalis  
> Created_at: 2017-06-05 20:47:13 UTC  
> Updated_at: 2017-06-05 21:54:13 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120205547  

>The lifetime of the transformed buffers is retained by the caller; ownership is not transferred.   
  
This reads somewhat weird. What does it mean for **lifetime** to be _retained_? **Controlled** may be a better choice here.

> Username: vinniefalco  
> Created_at: 2017-06-05 23:00:44 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120230717  

*"Control of buffers is retained by the caller; ownership is not transferred."*

> Username: vinniefalco  
> Created_at: 2017-06-05 23:00:50 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120230726  

willfix

---

📁 doc/3_3_buffers.qbk

```diff
  98 |+ ]]
  99 |+ [[
 100 |+     [link beast.ref.buffer_cat_view `buffer_cat_view`]
```

> Username: nbougalis  
> Created_at: 2017-06-05 20:48:49 UTC  
> Updated_at: 2017-06-05 21:54:13 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120205876  

Nit: `buffer_cat_view` and `buffer_prefix_view` aren't really "algorithms" per se.

> Username: vinniefalco  
> Created_at: 2017-06-05 23:01:40 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120230833  

Yeah I know but its the lesser of the evils


📁 doc/4_01_primer.qbk

```diff
   7 | 
   8 |- [section:primer HTTP Primer]
   8 |+ [section:primer Protocol Primer]
```

> Username: nbougalis  
> Created_at: 2017-06-05 20:58:19 UTC  
> Updated_at: 2017-06-05 21:54:13 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120208084  

Line 88: "It has specifies HTTP" the **has** is unnecessary and should be removed.


📁 doc/6_1_streams.qbk

```diff
  47 |+ 
  48 |+ If a socket type supports move construction, a websocket stream may be
  49 |+ constructed around the already existing socket by invoke the move
```

> Username: nbougalis  
> Created_at: 2017-06-05 21:17:45 UTC  
> Updated_at: 2017-06-05 21:54:13 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120212324  

"by invoke" should be "by invoking" or, better yet:

> Username: vinniefalco  
> Created_at: 2017-06-05 23:02:17 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120230909  

willfix


📁 doc/6_6_control.qbk

```diff
  59 |+ either synchronous read functions or asynchronous read functions will
  60 |+ invoke the ping callback, with the value of `is_pong` set to `true` if a
  61 |+ pong was received else `false` if a ping was received. The payload of
```

> Username: nbougalis  
> Created_at: 2017-06-05 21:25:41 UTC  
> Updated_at: 2017-06-05 21:54:14 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120214035  

'else `false' if a ping was received.` -> 'or `false` otherwise.'

> Username: vinniefalco  
> Created_at: 2017-06-05 22:50:06 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120229224  

great change

> Username: vinniefalco  
> Created_at: 2017-06-05 23:04:05 UTC  
> Updated_at: 2017-06-05 23:04:06 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120231150  

sketchy but I will change it anyway.


📁 doc/8_0_design.qbk

```diff
  18 |- 
  10 | The implementations are driven by business needs of cryptocurrency server
  11 | applications (e.g. [@https://ripple.com Ripple]) written in C++. These
```

> Username: nbougalis  
> Created_at: 2017-06-05 21:34:54 UTC  
> Updated_at: 2017-06-05 21:54:14 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120215986  

>applications (e.g. [@https://ripple.com Ripple]) written in C++.  
  
should be:  
  
>applications, such as [@https://github.com/ripple/rippled rippled], written in C++.   
  
Send people to the `rippled` github ;)

> Username: nbougalis  
> Created_at: 2017-06-05 21:40:24 UTC  
> Updated_at: 2017-06-05 21:54:14 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120216992  

Also, lines 24-25:  
  
>Leave important decisions to the user, such as allocating memory or  
>managing flow control.  
  
Should probably be:  
  
>Leave important decisions, such as allocating memory or  
>managing flow control, to to the user.

> Username: vinniefalco  
> Created_at: 2017-06-05 22:50:30 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120229298  

wow strong deja-vu there lol

> Username: vinniefalco  
> Created_at: 2017-06-05 23:04:10 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120231164  

willfix

---

📁 doc/8_0_design.qbk

```diff
  17 |- ''']
  18 |- 
  10 | The implementations are driven by business needs of cryptocurrency server
```

> Username: nbougalis  
> Created_at: 2017-06-05 21:35:52 UTC  
> Updated_at: 2017-06-05 21:54:14 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120216182  

"were originally driven by"

> Username: vinniefalco  
> Created_at: 2017-06-05 23:03:56 UTC  
> Url: https://github.com/boostorg/beast/pull/422#discussion_r120231129  

willfix


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-06-05 23:04:14 UTC  
> Url: https://github.com/boostorg/beast/pull/422#issuecomment-306334948  

Thanks

---
