# #599 Version 73 [Closed]

> Username: vinniefalco  
> Created at: 2017-07-05 05:30:02 UTC  
> Updated at: 2017-07-06 16:31:00 UTC  
> Closed at: 2017-07-06 16:22:06 UTC  
> Url: https://github.com/boostorg/beast/pull/599  

* Jamroot tweak  
* Verify certificates in SSL clients  
* Adjust benchmarks  
* Initialize local variable in basic_parser  
  
HTTP:  
  
* basic_parser optimizations  
* Add basic_parser tests  
  
API Changes:  
  
* Refactor header and message constructors  
* serializer::next replaces serializer::get  
  
Actions Required:  
  
* Evaluate each message constructor call site and  
  adjust the constructor argument list as needed.  
  
* Use serializer::next instead of serializer::get at call sites  
  
Documentation preview:  
http://vinniefalco.github.io/stage/beast/v73/

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-05 13:53:28 UTC  
> Updated_at: 2017-07-05 21:43:29 UTC  
> Url: https://github.com/boostorg/beast/pull/599#issuecomment-313109532  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=h1) Report  
> Merging [#599](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/9d082fd7d2d61e4d047eb09fe425620a4c2b6c91?src=pr&el=desc) will **increase** coverage by `0.1%`.  
> The diff coverage is `97.86%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/599/graphs/tree.svg?height=150&src=pr&token=Gq6MFA9JRF&width=650)](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop     #599     +/-   ##  
==========================================  
+ Coverage    93.78%   93.88%   +0.1%       
==========================================  
  Files           94       94               
  Lines         7368     7476    +108       
==========================================  
+ Hits          6910     7019    +109       
+ Misses         458      457      -1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/beast/http/serializer.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL3NlcmlhbGl6ZXIuaHBw) | `100% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/detail/rfc7230.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9yZmM3MjMwLmhwcA==) | `80.76% <ø> (-0.25%)` | :arrow_down: |  
| [include/beast/http/impl/serializer.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvc2VyaWFsaXplci5pcHA=) | `94.11% <ø> (ø)` | :arrow_up: |  
| [include/beast/http/impl/write.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvd3JpdGUuaXBw) | `90.59% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/message.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/beast/http/impl/message.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvbWVzc2FnZS5pcHA=) | `94.04% <100%> (+0.62%)` | :arrow_up: |  
| [include/beast/http/detail/basic\_parser.hpp](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `97.72% <97.35%> (-2.28%)` | :arrow_down: |  
| [include/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=tree#diff-aW5jbHVkZS9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `95.48% <98.01%> (-0.25%)` | :arrow_down: |  
| ... and [6 more](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=footer). Last update [9d082fd...59c8433](https://codecov.io/gh/vinniefalco/Beast/pull/599?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-07-06 00:05:57 UTC  
> Url: https://github.com/boostorg/beast/pull/599#issuecomment-313259436  

@octopus-prime Could you please look this over and let me know if you think it should be merged as-is?

---

## Comment 3

> Username: octopus-prime  
> Created_at: 2017-07-06 11:52:50 UTC  
> Url: https://github.com/boostorg/beast/pull/599#issuecomment-313374642  

BTW: could build on osx using bjam without problems.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2017-07-06 12:36:23 UTC  
> Url: https://github.com/boostorg/beast/pull/599#issuecomment-313383595  

Thanks, glad to hear it!!

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2017-07-06 16:22:06 UTC  
> Url: https://github.com/boostorg/beast/pull/599#issuecomment-313446461  

Merged

---
