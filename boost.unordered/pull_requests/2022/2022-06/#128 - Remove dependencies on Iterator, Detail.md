# #128 Remove dependencies on Iterator, Detail [Merged]

> Username: cmazakas  
> Created at: 2022-06-20 22:02:00 UTC  
> Updated at: 2022-06-21 19:05:38 UTC  
> Merged at: 2022-06-21 18:45:13 UTC  
> Closed at: 2022-06-21 18:45:13 UTC  
> Url: https://github.com/boostorg/unordered/pull/128  

Update test CML to include ConceptCheck as a dependency as it's no longer transitively included.

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-06-20 22:11:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/128#issuecomment-1160887953  

Use `conditional` from type_traits instead of declaring this MSVC 6 if-then thing inline maybe?

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-06-20 22:14:48 UTC  
> Url: https://github.com/boostorg/unordered/pull/128#issuecomment-1160890469  

You shouldn't need the user-declared copy constructor and destructor of `grouped_bucket_iterator`. The compiler-generated ones should be fine.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-06-20 22:15:55 UTC  
> Url: https://github.com/boostorg/unordered/pull/128#issuecomment-1160891342  

Same for `grouped_local_bucket_iterator`, including the copy assignment.

---

## Comment 4

> Username: pdimov  
> Created_at: 2022-06-20 22:16:48 UTC  
> Url: https://github.com/boostorg/unordered/pull/128#issuecomment-1160892082  

Same for `const_grouped_local_bucket_iterator`.

---

## Comment 5

> Username: pdimov  
> Created_at: 2022-06-20 22:17:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/128#issuecomment-1160892860  

Same for `iterator` and `c_iterator`.

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2022-06-21 01:00:27 UTC  
> Updated_at: 2022-06-21 15:49:59 UTC  
> Url: https://github.com/boostorg/unordered/pull/128#issuecomment-1161014738  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/128?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#128](https://codecov.io/gh/boostorg/unordered/pull/128?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2417ede) into [develop](https://codecov.io/gh/boostorg/unordered/commit/1ed2a0a0f1c7d94d24b503fec5a187aff73c21a6?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1ed2a0a) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 2417ede differs from pull request most recent head 90b2536. Consider uploading reports for the commit 90b2536 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/128/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/128?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #128   +/-   ##  
========================================  
  Coverage    97.30%   97.30%             
========================================  
  Files           73       73             
  Lines         9790     9883   +93       
========================================  
+ Hits          9526     9617   +91       
- Misses         264      266    +2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/128?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/helpers/random\_values.hpp](https://codecov.io/gh/boostorg/unordered/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9oZWxwZXJzL3JhbmRvbV92YWx1ZXMuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/unordered/detail/fca.hpp](https://codecov.io/gh/boostorg/unordered/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZjYS5ocHA=) | `99.31% <100.00%> (+0.11%)` | :arrow_up: |  
| [include/boost/unordered/detail/implementation.hpp](https://codecov.io/gh/boostorg/unordered/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ltcGxlbWVudGF0aW9uLmhwcA==) | `98.57% <100.00%> (+0.06%)` | :arrow_up: |  
| [test/objects/minimal.hpp](https://codecov.io/gh/boostorg/unordered/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9vYmplY3RzL21pbmltYWwuaHBw) | `82.14% <0.00%> (-1.20%)` | :arrow_down: |  
| [test/objects/test.hpp](https://codecov.io/gh/boostorg/unordered/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9vYmplY3RzL3Rlc3QuaHBw) | `94.28% <0.00%> (-0.48%)` | :arrow_down: |  
| [include/boost/unordered/unordered\_map.hpp](https://codecov.io/gh/boostorg/unordered/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX21hcC5ocHA=) | `99.84% <0.00%> (-0.01%)` | :arrow_down: |  
| [include/boost/unordered/unordered\_set.hpp](https://codecov.io/gh/boostorg/unordered/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX3NldC5ocHA=) | `98.91% <0.00%> (ø)` | |  
| [test/unordered/emplace\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/128/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvZW1wbGFjZV90ZXN0cy5jcHA=) | `96.82% <0.00%> (+0.06%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/128?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/128?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1ed2a0a...90b2536](https://codecov.io/gh/boostorg/unordered/pull/128?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Review 7 [Commented]

> Username: pdimov  
> Created_at: 2022-06-21 16:57:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/128#pullrequestreview-1013945194  

📁 include/boost/unordered/detail/implementation.hpp

```diff
1743 | 
1744 |           iterator() : p(), itb(){};
```

> Username: pdimov  
> Created_at: 2022-06-21 16:57:33 UTC  
> Url: https://github.com/boostorg/unordered/pull/128#discussion_r902857179  

Extra semicolon here.


---
