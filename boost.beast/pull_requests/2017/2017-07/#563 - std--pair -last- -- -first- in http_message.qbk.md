# #563 std::pair "last" -> "first" in http_message.qbk [Closed]

> Username: tzlaine  
> Created at: 2017-07-01 21:33:27 UTC  
> Updated at: 2017-07-03 21:51:56 UTC  
> Closed at: 2017-07-03 21:51:56 UTC  
> Url: https://github.com/boostorg/beast/pull/563  



---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-07-01 21:35:38 UTC  
> Url: https://github.com/boostorg/beast/pull/563#issuecomment-312456854  

How did this get past the editor's desk!!!

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2017-07-01 21:45:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/563#pullrequestreview-47537525  

📁 doc/9_1_http_message.qbk

```diff
 198 |- for the purposes of construction, except that instead of `first` and `last`
 198 |+ for the purposes of construction, except that instead of `first` and `second`
 199 | we have `fields` and `body`. This means that single-argument constructors
```

> Username: vinniefalco  
> Created_at: 2017-07-01 21:45:30 UTC  
> Url: https://github.com/boostorg/beast/pull/563#discussion_r125169589  

Actually line 199 is out of date. There is no more `fields` data member of `message`. Instead, the **Fields** template argument type is now a base class of `header` (which is a base class of `message`). I'd like your input on how this can be reworded to reflect the current state of affairs.


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2017-07-01 22:12:21 UTC  
> Updated_at: 2017-07-01 22:12:38 UTC  
> Url: https://github.com/boostorg/beast/pull/563#issuecomment-312458373  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/563?src=pr&el=h1) Report  
> Merging [#563](https://codecov.io/gh/vinniefalco/Beast/pull/563?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/78a065ba39836d91d7e70d93de7f9140f518083b?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/563/graphs/tree.svg?width=650&height=150&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/vinniefalco/Beast/pull/563?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #563   +/-   ##  
========================================  
  Coverage    93.77%   93.77%             
========================================  
  Files           94       94             
  Lines         7393     7393             
========================================  
  Hits          6933     6933             
  Misses         460      460  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/563?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/563?src=pr&el=footer). Last update [78a065b...250efa5](https://codecov.io/gh/vinniefalco/Beast/pull/563?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2017-07-02 14:23:19 UTC  
> Url: https://github.com/boostorg/beast/pull/563#issuecomment-312494772  

This will go into **v71**

---
