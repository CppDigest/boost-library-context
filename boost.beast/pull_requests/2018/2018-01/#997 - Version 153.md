# #997 Version 153 [Merged]

> Username: vinniefalco  
> Created at: 2018-01-24 22:28:22 UTC  
> Updated at: 2018-02-17 17:59:14 UTC  
> Merged at: 2018-01-25 22:07:34 UTC  
> Closed at: 2018-01-25 22:07:34 UTC  
> Url: https://github.com/boostorg/beast/pull/997  

* Remove BOOST_VERSION checks  
* Use make_error_code for setting an error_code from errc  
* Use boost::winapi::GetLastError() consistently   
* Update README.md for branches  
* Avoid string_view::clear  
* Fix iterator version of basic_fields::erase  
* Fix use-after-move in example request handlers  
* Add Bishop Fox interview media

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-01-25 01:45:26 UTC  
> Updated_at: 2018-01-25 18:31:40 UTC  
> Url: https://github.com/boostorg/beast/pull/997#issuecomment-360334087  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/997?src=pr&el=h1) Report  
> Merging [#997](https://codecov.io/gh/boostorg/beast/pull/997?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/be147865502feeac8c3feac18be6c255f8f45a97?src=pr&el=desc) will **decrease** coverage by `0.01%`.  
> The diff coverage is `95.65%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/997/graphs/tree.svg?token=Gq6MFA9JRF&src=pr&height=150&width=650)](https://codecov.io/gh/boostorg/beast/pull/997?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #997      +/-   ##  
===========================================  
- Coverage    95.45%   95.43%   -0.02%       
===========================================  
  Files          106      106                
  Lines        10601    10614      +13       
===========================================  
+ Hits         10119    10130      +11       
- Misses         482      484       +2  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/997?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/zlib/detail/inflate\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5ocHA=) | `88.39% <ø> (ø)` | :arrow_up: |  
| [...lude/boost/beast/websocket/detail/utf8\_checker.hpp](https://codecov.io/gh/boostorg/beast/pull/997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL3V0ZjhfY2hlY2tlci5ocHA=) | `99.23% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/serializer.ipp](https://codecov.io/gh/boostorg/beast/pull/997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `94.71% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/detail/mask.hpp](https://codecov.io/gh/boostorg/beast/pull/997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL21hc2suaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/verb.ipp](https://codecov.io/gh/boostorg/beast/pull/997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvdmVyYi5pcHA=) | `98.79% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/buffers\_suffix.hpp](https://codecov.io/gh/boostorg/beast/pull/997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2J1ZmZlcnNfc3VmZml4LmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/write.ipp](https://codecov.io/gh/boostorg/beast/pull/997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `93.29% <ø> (ø)` | :arrow_up: |  
| [...ude/boost/beast/core/impl/buffered\_read\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyZWRfcmVhZF9zdHJlYW0uaXBw) | `92.72% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/buffers\_prefix.hpp](https://codecov.io/gh/boostorg/beast/pull/997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2J1ZmZlcnNfcHJlZml4LmhwcA==) | `94.11% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/boostorg/beast/pull/997/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `92.37% <ø> (ø)` | :arrow_up: |  
| ... and [7 more](https://codecov.io/gh/boostorg/beast/pull/997/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/997?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/997?src=pr&el=footer). Last update [be14786...908f84c](https://codecov.io/gh/boostorg/beast/pull/997?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: p-kalugin  
> Created_at: 2018-01-25 06:26:04 UTC  
> Url: https://github.com/boostorg/beast/pull/997#issuecomment-360373460  

Typo: README.md gives same link (https://github.com/boostorg/beast/tree/master) for both master and develop branches.

---

## Review 3 [Approved]

> Username: djarek  
> Created_at: 2018-01-25 21:48:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/997#pullrequestreview-91683316  

LGTM

---
