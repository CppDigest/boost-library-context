# #103 Refactor value conversions [Closed]

> Username: sdkrystian  
> Created at: 2020-05-28 21:47:05 UTC  
> Updated at: 2020-08-22 18:01:38 UTC  
> Closed at: 2020-06-09 01:03:16 UTC  
> Url: https://github.com/boostorg/json/pull/103  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-05-28 21:51:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/103#pullrequestreview-420518469  

📁 doc/qbk/02_9_conversion.qbk

```diff
  21 |+   `void tag_invoke( to_value_tag, value&, T )`
  22 | 
  23 | * A specialization of __value_cast_traits__ for `T` containing
```

> Username: vinniefalco  
> Created_at: 2020-05-28 21:51:23 UTC  
> Updated_at: 2020-06-08 19:37:18 UTC  
> Url: https://github.com/boostorg/json/pull/103#discussion_r432144434  

`value_cast`?


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-05-28 21:53:18 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-635628343  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2020-05-28 22:33:11 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-635643387  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2020-05-28 22:38:11 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-635645042  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2020-05-28 22:43:04 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-635646663  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2020-05-28 23:53:11 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-635677132  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2020-05-29 00:23:12 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-635686238  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2020-05-29 00:38:11 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-635690477  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2020-05-29 01:13:10 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-635700015  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 10

> Username: codecov[bot]  
> Created_at: 2020-05-29 23:08:58 UTC  
> Updated_at: 2020-06-08 23:55:50 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-636230490  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/103?src=pr&el=h1) Report  
> Merging [#103](https://codecov.io/gh/CPPAlliance/json/pull/103?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/fcdf8856512b51109ec4fe6792a40302bb30768c&el=desc) will **increase** coverage by `0.08%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/103/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/103?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #103      +/-   ##  
===========================================  
+ Coverage    98.99%   99.07%   +0.08%       
===========================================  
  Files           59       60       +1       
  Lines         5183     5217      +34       
===========================================  
+ Hits          5131     5169      +38       
+ Misses          52       48       -4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/103?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/value\_ref.hpp](https://codecov.io/gh/CPPAlliance/json/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWVfcmVmLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_conversion.hpp](https://codecov.io/gh/CPPAlliance/json/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9jb252ZXJzaW9uLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/value\_from.hpp](https://codecov.io/gh/CPPAlliance/json/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9mcm9tLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://codecov.io/gh/CPPAlliance/json/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value\_from.hpp](https://codecov.io/gh/CPPAlliance/json/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX2Zyb20uaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value\_ref.hpp](https://codecov.io/gh/CPPAlliance/json/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3JlZi5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value\_to.hpp](https://codecov.io/gh/CPPAlliance/json/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3RvLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/103/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| ... and [3 more](https://codecov.io/gh/CPPAlliance/json/pull/103/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/103?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/103?src=pr&el=footer). Last update [fcdf885...99deedc](https://codecov.io/gh/CPPAlliance/json/pull/103?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2020-06-01 19:33:15 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-637059748  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2020-06-01 19:58:16 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-637070984  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2020-06-01 22:43:16 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-637163857  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2020-06-02 02:48:13 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-637237250  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2020-06-03 23:05:27 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-638506070  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2020-06-04 00:28:24 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-638529505  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 17

> Username: vinniefalco  
> Created_at: 2020-06-04 00:34:59 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-638531228  

This is not yet ready for review, it has a lot of errors

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2020-06-05 01:51:16 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639210370  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2020-06-05 01:58:17 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639212482  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2020-06-05 02:03:14 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639213784  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2020-06-05 03:33:14 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639237861  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2020-06-05 20:13:28 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639774887  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2020-06-05 20:28:27 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639789457  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2020-06-05 21:33:24 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639832803  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2020-06-05 21:53:18 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639843074  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2020-06-05 22:08:14 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639856500  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 27

> Username: cppalliance-bot  
> Created_at: 2020-06-05 22:28:15 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639867657  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 28

> Username: cppalliance-bot  
> Created_at: 2020-06-05 22:38:20 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639872463  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 29

> Username: cppalliance-bot  
> Created_at: 2020-06-05 23:18:10 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639889056  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 30

> Username: cppalliance-bot  
> Created_at: 2020-06-05 23:38:10 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639897236  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 31

> Username: cppalliance-bot  
> Created_at: 2020-06-06 00:08:10 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639908782  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 32

> Username: cppalliance-bot  
> Created_at: 2020-06-06 00:13:10 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639910611  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 33

> Username: cppalliance-bot  
> Created_at: 2020-06-06 00:23:11 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639913921  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 34

> Username: cppalliance-bot  
> Created_at: 2020-06-06 00:38:10 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639919205  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 35

> Username: cppalliance-bot  
> Created_at: 2020-06-06 00:58:09 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639929991  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 36

> Username: cppalliance-bot  
> Created_at: 2020-06-06 01:03:12 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639932011  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 37

> Username: cppalliance-bot  
> Created_at: 2020-06-06 01:08:11 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639934182  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 38

> Username: cppalliance-bot  
> Created_at: 2020-06-06 01:13:16 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-639936282  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 39

> Username: cppalliance-bot  
> Created_at: 2020-06-08 17:00:04 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-640751780  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Review 40 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-08 17:08:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/103#pullrequestreview-426444493  

📁 doc/qbk/02_9_conversion.qbk

```diff
  17 |- conversions between objects of type `T` and a JSON value by:
  16 |+ types. Conversions from a type `T` to __value__ are done by __value_from__.
  17 |+ Converting in the opposite direction is done with __value_to__.
```

> Username: vinniefalco  
> Created_at: 2020-06-08 17:08:51 UTC  
> Updated_at: 2020-06-08 19:37:18 UTC  
> Url: https://github.com/boostorg/json/pull/103#discussion_r436861360  

This avoids the switch from singular to plural:  
  
"A conversion from a type `T` to __value__ is done by __value_from__. The conversion in the opposite direction is done with __value_to__."


---

## Review 41 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-08 17:09:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/103#pullrequestreview-426445103  

📁 doc/qbk/02_9_conversion.qbk

```diff
  38 |- When more than one conversion is available for a given type, one
  39 |- will be chosen according to a defined set of priorities.
  39 |+ Since the type being converted to/from is embedded into the
```

> Username: vinniefalco  
> Created_at: 2020-06-08 17:09:39 UTC  
> Updated_at: 2020-06-08 19:37:18 UTC  
> Url: https://github.com/boostorg/json/pull/103#discussion_r436861821  

Remove "to/from"


---

## Review 42 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-08 17:09:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/103#pullrequestreview-426445362  

📁 doc/qbk/02_9_conversion.qbk

```diff
  40 |+ parameter-type-list of the function, user-provided overloads
  41 |+ are visible to argument-dependent lookup and will be considered
  42 |+ when a conversion is performed. Consider the following:
```

> Username: vinniefalco  
> Created_at: 2020-06-08 17:09:58 UTC  
> Updated_at: 2020-06-08 19:37:18 UTC  
> Url: https://github.com/boostorg/json/pull/103#discussion_r436862021  

Using the word "consider" in two sentences in a row.


---

## Review 43 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-08 17:10:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/103#pullrequestreview-426446069  

📁 doc/qbk/02_9_conversion.qbk

```diff
  64 |- When conversion between `T` and __value__ is defined in both directions,
  65 |- round trips are possible:
  74 |+ [heading Converting to __value__]
```

> Username: vinniefalco  
> Created_at: 2020-06-08 17:10:51 UTC  
> Updated_at: 2020-06-08 19:37:18 UTC  
> Url: https://github.com/boostorg/json/pull/103#discussion_r436862573  

Don't generate links in headings, use this instead:  
```  
[heading Converting to json::value]  
```  
  
Quickbook can get weird with formatting otherwise.


---

## Review 44 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-08 17:11:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/103#pullrequestreview-426446890  

📁 doc/qbk/main.qbk

```diff
  63 |+ [def __value_to__               [link json.ref.boost__json__value_to `value_to`]]
  64 |+ [def __has_value_from__         [link json.ref.boost__json__has_value_from `has_value_from`]]
  65 |+ [def __has_value_to__           [link json.ref.boost__json__has_value_to `has_value_to`]]
```

> Username: vinniefalco  
> Created_at: 2020-06-08 17:11:49 UTC  
> Updated_at: 2020-06-08 19:37:18 UTC  
> Url: https://github.com/boostorg/json/pull/103#discussion_r436863208  

please keep these in alphabetical order


---

## Review 45 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-08 17:12:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/103#pullrequestreview-426447716  

📁 include/boost/json.hpp

```diff
  27 | #include <boost/json/value.hpp>
  29 |- #include <boost/json/value_cast.hpp>
  28 |+ #include <boost/json/value_to.hpp>
```

> Username: vinniefalco  
> Created_at: 2020-06-08 17:12:45 UTC  
> Updated_at: 2020-06-08 19:37:18 UTC  
> Url: https://github.com/boostorg/json/pull/103#discussion_r436863848  

Please keep these includes in alphabetical order


---

## Comment 46

> Username: cppalliance-bot  
> Created_at: 2020-06-08 17:13:17 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-640759509  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Review 47 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-08 17:13:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/103#pullrequestreview-426448381  

📁 include/boost/json/detail/value_conversion.hpp

```diff
   8 |+ //
   9 |+ 
  10 |+ #ifndef BOOST_JSON_DETAIL_VALUE_CONVERSION_HPP
```

> Username: vinniefalco  
> Created_at: 2020-06-08 17:13:30 UTC  
> Updated_at: 2020-06-08 19:37:18 UTC  
> Url: https://github.com/boostorg/json/pull/103#discussion_r436864327  

Please rename this file to `value_traits.hpp` (since it consists mostly of metafunctions)


---

## Review 48 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-08 17:16:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/103#pullrequestreview-426451173  

📁 include/boost/json/detail/value_from.hpp

```diff
  29 |+ template<std::size_t Max, std::size_t Idx = 0, typename T>
  30 |+ void
  31 |+ tuple_to_array(T&& tup, array& input)
```

> Username: vinniefalco  
> Created_at: 2020-06-08 17:16:49 UTC  
> Updated_at: 2020-06-08 19:37:18 UTC  
> Url: https://github.com/boostorg/json/pull/103#discussion_r436866537  

`Idx` -> `I` or `Index`  
`tup` -> `t`  
`input` -> `v` or `a`

> Username: sdkrystian  
> Created_at: 2020-06-08 17:40:25 UTC  
> Updated_at: 2020-06-08 19:37:18 UTC  
> Url: https://github.com/boostorg/json/pull/103#discussion_r436881390  

`input` -> `res`?


---

## Review 49 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-08 17:18:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/103#pullrequestreview-426452758  

📁 include/boost/json/detail/value_from.hpp

```diff
  52 |+ tag_invoke(
  53 |+     value_from_tag,
  54 |+     value& result,
```

> Username: vinniefalco  
> Created_at: 2020-06-08 17:18:49 UTC  
> Updated_at: 2020-06-08 19:37:18 UTC  
> Url: https://github.com/boostorg/json/pull/103#discussion_r436867757  

`result` -> `jv` ("jv" is always the name given to variables of type `value`)


---

## Review 50 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-08 17:19:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/103#pullrequestreview-426453082  

📁 include/boost/json/detail/value_from.hpp

```diff
 121 |+ tag_invoke(
 122 |+     value_from_tag,
 123 |+     value& result,
```

> Username: vinniefalco  
> Created_at: 2020-06-08 17:19:13 UTC  
> Updated_at: 2020-06-08 19:37:18 UTC  
> Url: https://github.com/boostorg/json/pull/103#discussion_r436868001  

`jv`


---

## Review 51 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-08 17:21:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/103#pullrequestreview-426454942  

📁 include/boost/json/detail/value_to.hpp

```diff
  22 |+ 
  23 |+ template<class>
  24 |+ struct value_to_tag { };
```

> Username: vinniefalco  
> Created_at: 2020-06-08 17:21:31 UTC  
> Updated_at: 2020-06-08 19:37:18 UTC  
> Url: https://github.com/boostorg/json/pull/103#discussion_r436869514  

We have a documentation problem here, because `value_to_tag` needs to be in the documentation as a linkable item, and it needs an entry in the quick reference. So it needs to be in a public header (i.e. not a header in detail). You will need to move it and figure this out. Same for `value_from_tag`.


---

## Comment 52

> Username: cppalliance-bot  
> Created_at: 2020-06-08 18:18:23 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-640791912  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 53

> Username: cppalliance-bot  
> Created_at: 2020-06-08 19:08:07 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-640816240  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 54

> Username: cppalliance-bot  
> Created_at: 2020-06-08 19:38:20 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-640844262  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 55

> Username: cppalliance-bot  
> Created_at: 2020-06-08 19:43:21 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-640847413  

An automated preview of the documentation is available at [http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://103.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 56

> Username: vinniefalco  
> Created_at: 2020-06-09 01:03:16 UTC  
> Url: https://github.com/boostorg/json/pull/103#issuecomment-640966511  

Merged thanks

---
