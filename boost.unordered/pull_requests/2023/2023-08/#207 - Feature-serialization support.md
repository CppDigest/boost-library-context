# #207 Feature/serialization support [Merged]

> Username: joaquintides  
> Created at: 2023-08-17 08:51:26 UTC  
> Updated at: 2024-09-17 21:07:10 UTC  
> Merged at: 2023-08-29 17:50:21 UTC  
> Closed at: 2023-08-29 17:50:21 UTC  
> Url: https://github.com/boostorg/unordered/pull/207  

If we're OK with this PR, we need to sync up its merging with the removal of serialization functionality in  
  
https://github.com/boostorg/serialization/blob/boost-1.83.0/include/boost/serialization/boost_unordered_map.hpp  
https://github.com/boostorg/serialization/blob/boost-1.83.0/include/boost/serialization/boost_unordered_set.hpp  
  
I can contact Robert for this as soon as the PR is approved. Thank you!

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-08-17 09:02:03 UTC  
> Url: https://github.com/boostorg/unordered/pull/207#issuecomment-1681905204  

An automated preview of the documentation is available at [https://207.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://207.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-08-18 08:47:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/207#issuecomment-1683578495  

An automated preview of the documentation is available at [https://207.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://207.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 3 [Commented]

> Username: cmazakas  
> Created_at: 2023-08-18 15:31:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/207#pullrequestreview-1584790976  

📁 test/objects/test.hpp

```diff
   2 | // Copyright 2006-2009 Daniel James.
   3 | // Copyright 2022 Christian Mazakas
   4 |+ // Copyright 2023 Joaqui M Lopez Munoz
```

> Username: cmazakas  
> Created_at: 2023-08-18 15:31:29 UTC  
> Updated_at: 2023-08-18 15:31:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/207#discussion_r1298589175  

I think we're missing an `n` here.

> Username: joaquintides  
> Created_at: 2023-08-18 19:09:20 UTC  
> Url: https://github.com/boostorg/unordered/pull/207#discussion_r1298776060  

Fixed!


---

## Review 4 [Commented]

> Username: cmazakas  
> Created_at: 2023-08-18 16:55:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/207#pullrequestreview-1584908905  

📁 include/boost/unordered/detail/archive_constructed.hpp

```diff
  34 |+     }
  35 |+     BOOST_CATCH(...){
  36 |+       (&get())->~T();
```

> Username: cmazakas  
> Created_at: 2023-08-18 16:55:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/207#discussion_r1298666471  

Out of curiosity, is there a reason to use `(&get())->~T();` over something like `get().~T();`?

> Username: joaquintides  
> Created_at: 2023-08-18 19:09:17 UTC  
> Updated_at: 2023-08-18 19:13:11 UTC  
> Url: https://github.com/boostorg/unordered/pull/207#discussion_r1298776002  

No reason, I copied this code from some other library of mine. Simplified as suggested, plus s/`&`/`boost::addressof` in the remaining cases.


---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2023-08-18 17:18:34 UTC  
> Updated_at: 2024-09-17 21:07:10 UTC  
> Url: https://github.com/boostorg/unordered/pull/207#issuecomment-1684211063  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/207?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `98.26590%` with `6 lines` in your changes missing coverage. Please review.  
> Project coverage is 97.91%. Comparing base [(`bcd8969`)](https://app.codecov.io/gh/boostorg/unordered/commit/bcd8969b9ae163b79804091daea42a4df756d3cd?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ca2a46f`)](https://app.codecov.io/gh/boostorg/unordered/commit/ca2a46f290b28eec6672292fbf347f6d192c5a1c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 327 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/unordered/pull/207?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [...ude/boost/unordered/detail/archive\_constructed.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Farchive_constructed.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2FyY2hpdmVfY29uc3RydWN0ZWQuaHBw) | 75.00% | [3 Missing :warning: ](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [...e/boost/unordered/detail/bad\_archive\_exception.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Fbad_archive_exception.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2JhZF9hcmNoaXZlX2V4Y2VwdGlvbi5ocHA=) | 0.00% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/unordered/detail/implementation.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Fimplementation.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ltcGxlbWVudGF0aW9uLmhwcA==) | 96.00% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [...ost/unordered/detail/serialize\_tracked\_address.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Fserialize_tracked_address.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL3NlcmlhbGl6ZV90cmFja2VkX2FkZHJlc3MuaHBw) | 94.73% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/207/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #207      +/-   ##  
===========================================  
+ Coverage    97.89%   97.91%   +0.01%       
===========================================  
  Files          130      138       +8       
  Lines        19053    19412     +359       
===========================================  
+ Hits         18652    19007     +355       
- Misses         401      405       +4       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/unordered/pull/207?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/concurrent\_flat\_map.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fconcurrent_flat_map.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvY29uY3VycmVudF9mbGF0X21hcC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/unordered/detail/fca.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Ffca.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZjYS5ocHA=) | `98.95% <100.00%> (+0.02%)` | :arrow_up: |  
| [...de/boost/unordered/detail/foa/concurrent\_table.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Ffoa%2Fconcurrent_table.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb25jdXJyZW50X3RhYmxlLmhwcA==) | `99.63% <100.00%> (+0.03%)` | :arrow_up: |  
| [include/boost/unordered/detail/foa/core.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Ffoa%2Fcore.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb3JlLmhwcA==) | `100.00% <ø> (+0.17%)` | :arrow_up: |  
| [...lude/boost/unordered/detail/foa/flat\_map\_types.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Ffoa%2Fflat_map_types.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9mbGF0X21hcF90eXBlcy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/unordered/detail/foa/table.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Ffoa%2Ftable.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS90YWJsZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [...e/boost/unordered/detail/serialization\_version.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Fserialization_version.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL3NlcmlhbGl6YXRpb25fdmVyc2lvbi5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [...ude/boost/unordered/detail/serialize\_container.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Fserialize_container.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL3NlcmlhbGl6ZV9jb250YWluZXIuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...boost/unordered/detail/serialize\_fca\_container.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Fserialize_fca_container.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL3NlcmlhbGl6ZV9mY2FfY29udGFpbmVyLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/unordered/unordered\_flat\_map.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Funordered_flat_map.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX2ZsYXRfbWFwLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| ... and [13 more](https://app.codecov.io/gh/boostorg/unordered/pull/207?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [6 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/unordered/pull/207/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/207?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/207?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [bcd8969...ca2a46f](https://app.codecov.io/gh/boostorg/unordered/pull/207?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-08-18 19:17:05 UTC  
> Url: https://github.com/boostorg/unordered/pull/207#issuecomment-1684338383  

An automated preview of the documentation is available at [https://207.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://207.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-08-25 09:42:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/207#issuecomment-1693081975  

An automated preview of the documentation is available at [https://207.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://207.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
