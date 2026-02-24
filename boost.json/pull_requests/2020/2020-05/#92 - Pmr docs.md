# #92 Pmr docs [Closed]

> Username: sdkrystian  
> Created at: 2020-05-15 23:38:55 UTC  
> Updated at: 2020-06-08 19:24:16 UTC  
> Closed at: 2020-05-21 18:00:07 UTC  
> Url: https://github.com/boostorg/json/pull/92  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-05-15 23:43:18 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-629550548  

An automated preview of the documentation is available at [http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-05-17 00:38:01 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-629725223  

An automated preview of the documentation is available at [http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2020-05-17 18:32:57 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-629841171  

An automated preview of the documentation is available at [http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2020-05-17 19:07:57 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-629845442  

An automated preview of the documentation is available at [http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2020-05-17 19:17:57 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-629846672  

An automated preview of the documentation is available at [http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2020-05-17 19:32:57 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-629848642  

An automated preview of the documentation is available at [http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2020-05-18 01:37:59 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-629900247  

An automated preview of the documentation is available at [http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2020-05-18 18:43:18 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-630367993  

An automated preview of the documentation is available at [http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2020-05-18 19:13:10 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-630383328  

An automated preview of the documentation is available at [http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2020-05-18 20:38:16 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-630422756  

An automated preview of the documentation is available at [http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2020-05-18 20:58:16 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-630431590  

An automated preview of the documentation is available at [http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-18 21:39:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/92#pullrequestreview-413957258  

📁 doc/qbk/02_6_allocators.qbk

```diff
  25 | * An implementation can indicate to the library that deallocation
  26 |-   is not necessary, improving the performance of destructors.
  26 |+   is not necessary, allowing destructor calls to be elided in certain situations.
```

> Username: vinniefalco  
> Created_at: 2020-05-18 21:39:08 UTC  
> Updated_at: 2020-05-19 20:43:08 UTC  
> Url: https://github.com/boostorg/json/pull/92#discussion_r426907538  

"elided" yuck, use normal words! "allowing calls to destructors for elements of containers to be skipped in most cases."


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-18 21:55:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/92#pullrequestreview-413965611  

📁 doc/qbk/02_6_allocators.qbk

```diff
  52 |+     [__make_counted_resource__]
  53 |+     [
  54 |+         Function to construct a reference-counted smart pointer
```

> Username: vinniefalco  
> Created_at: 2020-05-18 21:55:33 UTC  
> Updated_at: 2020-05-19 20:43:08 UTC  
> Url: https://github.com/boostorg/json/pull/92#discussion_r426914303  

This function returns...


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-18 21:56:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/92#pullrequestreview-413966231  

📁 doc/qbk/02_6_allocators.qbk

```diff
   1 |+ [/
   2 |+     Copyright (c) 2019 Vinnie Falco (vinnie.falco@gmail.com)
   3 |+ 	Copyright (c) 2020 Krystian Stasiowski (sdkrystian@gmail.com)
```

> Username: vinniefalco  
> Created_at: 2020-05-18 21:56:48 UTC  
> Updated_at: 2020-05-19 20:43:08 UTC  
> Url: https://github.com/boostorg/json/pull/92#discussion_r426914779  

TAB


---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2020-05-19 19:38:00 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-631038532  

An automated preview of the documentation is available at [http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 16

> Username: vinniefalco  
> Created_at: 2020-05-19 19:49:52 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-631044617  

Please squash this down to one commit

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2020-05-19 20:07:57 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-631054050  

An automated preview of the documentation is available at [http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2020-05-19 20:23:00 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-631061507  

An automated preview of the documentation is available at [http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2020-05-19 20:49:20 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-631074475  

An automated preview of the documentation is available at [http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://92.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 20

> Username: codecov[bot]  
> Created_at: 2020-05-20 03:37:41 UTC  
> Updated_at: 2020-05-20 04:48:18 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-631216558  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/92?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@e49288b`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/92/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/92?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #92   +/-   ##  
==========================================  
  Coverage           ?   98.99%             
==========================================  
  Files              ?       59             
  Lines              ?     5183             
  Branches           ?        0             
==========================================  
  Hits               ?     5131             
  Misses             ?       52             
  Partials           ?        0             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/92?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/array.hpp](https://codecov.io/gh/CPPAlliance/json/pull/92/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/92/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/object.hpp](https://codecov.io/gh/CPPAlliance/json/pull/92/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/serializer.hpp](https://codecov.io/gh/CPPAlliance/json/pull/92/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3NlcmlhbGl6ZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/storage\_ptr.hpp](https://codecov.io/gh/CPPAlliance/json/pull/92/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0b3JhZ2VfcHRyLmhwcA==) | `96.66% <ø> (ø)` | |  
| [include/boost/json/string.hpp](https://codecov.io/gh/CPPAlliance/json/pull/92/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/to\_value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/92/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3RvX3ZhbHVlLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/92/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/92?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/92?src=pr&el=footer). Last update [e49288b...4aabc3c](https://codecov.io/gh/CPPAlliance/json/pull/92?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 21

> Username: sdkrystian  
> Created_at: 2020-05-21 18:00:06 UTC  
> Url: https://github.com/boostorg/json/pull/92#issuecomment-632255675  

Merged with #94

---
