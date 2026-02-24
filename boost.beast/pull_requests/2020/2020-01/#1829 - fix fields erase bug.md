# #1829 fix fields erase bug [Closed]

> Username: madmongo1  
> Created at: 2020-01-29 22:59:18 UTC  
> Updated at: 2020-02-05 14:40:52 UTC  
> Closed at: 2020-02-05 14:40:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1829  

fixes #1828

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-01-30 01:16:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1829#pullrequestreview-350531558  

📁 include/boost/beast/http/impl/fields.hpp

```diff
 619 |     auto& e = *next++;
 620 |-     set_.erase(e);
 620 |+     set_.erase(set_.iterator_to(e));
```

> Username: vinniefalco  
> Created_at: 2020-01-30 01:16:28 UTC  
> Updated_at: 2020-02-04 16:42:09 UTC  
> Url: https://github.com/boostorg/beast/pull/1829#discussion_r372716796  

I'm a bit disappointed that `set_.erase(e);` is allowed to compile and then malfunction

> Username: madmongo1  
> Created_at: 2020-01-30 07:13:55 UTC  
> Updated_at: 2020-02-04 16:42:09 UTC  
> Url: https://github.com/boostorg/beast/pull/1829#discussion_r372788588  

One of the dangers of composition through inheritance.


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-01-30 01:17:15 UTC  
> Url: https://github.com/boostorg/beast/pull/1829#issuecomment-580039820  

"fix fields erase bug" is redundant. Prefer "fix erase field"

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2020-01-30 06:42:26 UTC  
> Updated_at: 2020-02-04 16:56:20 UTC  
> Url: https://github.com/boostorg/beast/pull/1829#issuecomment-580106460  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1829?src=pr&el=h1) Report  
> Merging [#1829](https://codecov.io/gh/boostorg/beast/pull/1829?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c9d5049307925bccaf2f5e57c127db1a1c78e178?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1829/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1829?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1829      +/-   ##  
===========================================  
- Coverage    95.19%   95.18%   -0.01%       
===========================================  
  Files          156      156                
  Lines        11956    11950       -6       
===========================================  
- Hits         11381    11375       -6       
  Misses         575      575  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1829?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/1829/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmhwcA==) | `97.57% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/1829/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0%> (-0.9%)` | :arrow_down: |  
| [include/boost/beast/zlib/detail/inflate\_stream.ipp](https://codecov.io/gh/boostorg/beast/pull/1829/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2RldGFpbC9pbmZsYXRlX3N0cmVhbS5pcHA=) | `94.37% <0%> (-0.2%)` | :arrow_down: |  
| [include/boost/beast/http/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/1829/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <0%> (+7.69%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1829?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1829?src=pr&el=footer). Last update [c9d5049...5b1ad50](https://codecov.io/gh/boostorg/beast/pull/1829?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: madmongo1  
> Created_at: 2020-01-30 12:13:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1829#issuecomment-580224897  

all comments addressed

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-04 16:05:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1829#pullrequestreview-353099557  

📁 test/beast/http/fields.cpp

```diff
1007 |     run() override
1008 |     {
1009 |+         testIssue1828();
```

> Username: vinniefalco  
> Created_at: 2020-02-04 16:05:27 UTC  
> Updated_at: 2020-02-04 16:42:09 UTC  
> Url: https://github.com/boostorg/beast/pull/1829#discussion_r374764912  

The order of calls should match the order of function declarations/definitions. Since you put `testIssue1828()` after all the other functions (which is the right thing to do), it should be called after all the other functions.

> Username: madmongo1  
> Created_at: 2020-02-04 16:44:01 UTC  
> Updated_at: 2020-02-04 16:44:02 UTC  
> Url: https://github.com/boostorg/beast/pull/1829#discussion_r374789165  

done


---
