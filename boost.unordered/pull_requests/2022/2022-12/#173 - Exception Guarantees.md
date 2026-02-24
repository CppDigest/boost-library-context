# #173 Exception Guarantees [Merged]

> Username: cmazakas  
> Created at: 2022-12-03 00:10:55 UTC  
> Updated at: 2022-12-13 22:22:33 UTC  
> Merged at: 2022-12-13 22:22:30 UTC  
> Closed at: 2022-12-13 22:22:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/173  

Update assignment operators and swap implementation to require noexcept swapability of the Hash, KeyEqual params. Correspondingly update tests as needed.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2022-12-03 00:16:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/173#pullrequestreview-1203255567  

📁 include/boost/unordered/detail/foa.hpp

```diff
1230 |+       using std::swap;
1231 |+       swap(h(),tmp_h);
1232 |+       swap(pred(),tmp_p);
```

> Username: pdimov  
> Created_at: 2022-12-03 00:16:10 UTC  
> Updated_at: 2022-12-03 00:16:11 UTC  
> Url: https://github.com/boostorg/unordered/pull/173#discussion_r1038643801  

Explanatory comments are missing here. "// can throw" on the tmp lines, then "// swap operations don't throw, so we know we have consistent hasher and key_equal at this point." Or even more verbose.

> Username: cmazakas  
> Created_at: 2022-12-05 21:11:48 UTC  
> Url: https://github.com/boostorg/unordered/pull/173#discussion_r1040100064  

Hopefully these comments offer a bit more clarity.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2022-12-03 00:18:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/173#pullrequestreview-1203256391  

📁 test/exception/swap_exception_tests.cpp

```diff
  24 |+     (void)line;
  25 |+ 
  26 |+     throw std::runtime_error("rawr!");
```

> Username: pdimov  
> Created_at: 2022-12-03 00:18:48 UTC  
> Url: https://github.com/boostorg/unordered/pull/173#discussion_r1038644518  

Not very professional. :-)


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2022-12-03 00:20:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/173#pullrequestreview-1203256876  

📁 test/exception/swap_exception_tests.cpp

```diff
 272 |+ // FOA containers deliberately choose to not offer the strong exception
 273 |+ // guarantee so we can't reliably test what happens if swapping one of the data
 274 |+ // members throws as the basic guarantee doesn't uphold invariants
```

> Username: pdimov  
> Created_at: 2022-12-03 00:20:12 UTC  
> Updated_at: 2022-12-03 00:20:13 UTC  
> Url: https://github.com/boostorg/unordered/pull/173#discussion_r1038644912  

The basic guarantee upholds invariants.


---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-12-03 02:34:57 UTC  
> Updated_at: 2022-12-13 22:02:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/173#issuecomment-1336018680  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/173?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#173](https://codecov.io/gh/boostorg/unordered/pull/173?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9f49fbf) into [develop](https://codecov.io/gh/boostorg/unordered/commit/6be2bf89b6aa1b05582ae0be4da08f0efefa1da1?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6be2bf8) will **decrease** coverage by `0.14%`.  
> The diff coverage is `83.33%`.  
  
> :exclamation: Current head 9f49fbf differs from pull request most recent head 0ad6ccb. Consider uploading reports for the commit 0ad6ccb to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/173/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/173?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #173      +/-   ##  
===========================================  
- Coverage    97.68%   97.54%   -0.15%       
===========================================  
  Files           83       83                
  Lines        12073    12085      +12       
===========================================  
- Hits         11794    11788       -6       
- Misses         279      297      +18       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/173?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/helpers/invariants.hpp](https://codecov.io/gh/boostorg/unordered/pull/173/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9oZWxwZXJzL2ludmFyaWFudHMuaHBw) | `75.00% <ø> (ø)` | |  
| [test/objects/minimal.hpp](https://codecov.io/gh/boostorg/unordered/pull/173/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9vYmplY3RzL21pbmltYWwuaHBw) | `82.55% <ø> (-0.40%)` | :arrow_down: |  
| [test/unordered/noexcept\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/173/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvbm9leGNlcHRfdGVzdHMuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/exception/swap\_exception\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/173/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9leGNlcHRpb24vc3dhcF9leGNlcHRpb25fdGVzdHMuY3Bw) | `79.48% <68.29%> (-18.02%)` | :arrow_down: |  
| [include/boost/unordered/detail/foa.hpp](https://codecov.io/gh/boostorg/unordered/pull/173/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS5ocHA=) | `97.94% <100.00%> (-0.69%)` | :arrow_down: |  
| [test/exception/assign\_exception\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/173/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9leGNlcHRpb24vYXNzaWduX2V4Y2VwdGlvbl90ZXN0cy5jcHA=) | `95.71% <100.00%> (+0.55%)` | :arrow_up: |  
| [test/exception/move\_assign\_exception\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/173/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9leGNlcHRpb24vbW92ZV9hc3NpZ25fZXhjZXB0aW9uX3Rlc3RzLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/objects/exception.hpp](https://codecov.io/gh/boostorg/unordered/pull/173/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9vYmplY3RzL2V4Y2VwdGlvbi5ocHA=) | `97.24% <100.00%> (+0.07%)` | :arrow_up: |  
| ... and [3 more](https://codecov.io/gh/boostorg/unordered/pull/173/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/173?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/173?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [01deb2f...0ad6ccb](https://codecov.io/gh/boostorg/unordered/pull/173?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 5 [Commented]

> Username: joaquintides  
> Created_at: 2022-12-03 11:29:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/173#pullrequestreview-1203454960  

* Why are you using `BOOST_STATIC_ASSERT` instead of plain `static_assert`?  
* Move assignment: you're swapping `h`  and `pred`, which feels incorrect to me (the source container need not keep target objects). I'd rather do:  
```  
hasher    tmp_h=std::move(x.h());  
key_equal tmp_p=std::move(x.pred());  
  
using std::swap;  
swap(h(),tmp_h);  
swap(pred(),tmp_p);  
```  
* You've replaced `swap_if<pocs>(al(),x.al())` with `swap(al(),x.al())`: I think this is incorrect, as the standard [says](https://eel.is/c++draft/container.requirements#container.reqmts-65) that allocators must we swappable only if `allocator_traits<allocator_type>::propagate_on_container_swap::value` is `true`. To be clear, if allocators are not swappable, the expression `swap(al(),x.al())` can legitimately fail to compile.  
* Docs should be updated to, at least, reflect the new `noexcept` conditions.

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-12-05 21:21:07 UTC  
> Url: https://github.com/boostorg/unordered/pull/173#issuecomment-1338184368  

An automated preview of the documentation is available at [https://173.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://173.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 7

> Username: cmazakas  
> Created_at: 2022-12-05 21:56:17 UTC  
> Url: https://github.com/boostorg/unordered/pull/173#issuecomment-1338228027  

> Why are you using BOOST_STATIC_ASSERT instead of plain static_assert?  
  
It was so that I didn't have to come up with an error message. I think only C++17 allows plain `static_assert(condition);`.  
  
> Move assignment: you're swapping h and pred, which feels incorrect to me (the source container need not keep target objects). I'd rather do:  
  
This becomes at-odds with our new `noexcept()` requirements, as I've found via the failing `noexcept_tests`. Technically, this is an odd-but-valid way of implementing noexcept move passively from our noexcept swap without imposing any new requirements on the Hash, KeyEqual params.  
  
> Docs should be updated to, at least, reflect the new noexcept conditions.  
Done!

---

## Comment 8

> Username: joaquintides  
> Created_at: 2022-12-06 10:12:27 UTC  
> Updated_at: 2022-12-06 18:59:35 UTC  
> Url: https://github.com/boostorg/unordered/pull/173#issuecomment-1339083437  

* `BOOST_STATIC_ASSERT`: I still prefer plain `static_assert` since C++11 is assumed, but this is a stylistic issue anyway. You're missing the `#include <boost/static_assert.hpp>`  in `foa.hpp`.   
* ~~Move assignment: I'd rather keep the old `noexcept` specification because this is what the standard explicitly has, and use the `swap` trick only as an internal way to guarantee basic safety (so, doing first a move-assignment of the objects to local variables). We can change the `noexcept` declaration when the standard does, which presumably will be in the direction of requiring non-throwing move assignability of `hash` and `pred` (making our trick superfluous).~~  
* Docs: the `noexcept`s in the _synopses_ are not in sync with those in the detailed explanations for `swap` and move assignment. ~~If the _current_ implementation of move assignment in this PR prevails, then t~~The statement _"move-assigns the hash function and predicate from other"_ is incorrect.

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-12-06 22:11:13 UTC  
> Url: https://github.com/boostorg/unordered/pull/173#issuecomment-1340076682  

An automated preview of the documentation is available at [https://173.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://173.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-12-06 22:17:13 UTC  
> Url: https://github.com/boostorg/unordered/pull/173#issuecomment-1340082742  

An automated preview of the documentation is available at [https://173.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://173.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 11 [Commented]

> Username: joaquintides  
> Created_at: 2022-12-07 07:53:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/173#pullrequestreview-1207930338  

📁 include/boost/unordered/detail/foa.hpp

```diff
  71 |+   static_assert(boost::is_nothrow_swappable<Hash>::value,                      \
  72 |+     "Template parameter Hash is required to be nothrow Swappable.");           \
  73 |+   static_assert(boost::is_nothrow_swappable<Pred>::value,                      \
```

> Username: joaquintides  
> Created_at: 2022-12-07 07:53:17 UTC  
> Url: https://github.com/boostorg/unordered/pull/173#discussion_r1041869943  

s/Swappable/swappable


---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-12-07 17:26:21 UTC  
> Url: https://github.com/boostorg/unordered/pull/173#issuecomment-1341316353  

An automated preview of the documentation is available at [https://173.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://173.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-12-09 18:16:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/173#issuecomment-1344616513  

An automated preview of the documentation is available at [https://173.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://173.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2022-12-13 22:11:05 UTC  
> Url: https://github.com/boostorg/unordered/pull/173#issuecomment-1349864306  

An automated preview of the documentation is available at [https://173.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://173.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
