# #176 feature/foa fast copy fix [Merged]

> Username: joaquintides  
> Created at: 2022-12-12 20:40:40 UTC  
> Updated at: 2024-08-17 16:49:16 UTC  
> Merged at: 2022-12-13 21:52:59 UTC  
> Closed at: 2022-12-13 21:52:59 UTC  
> Url: https://github.com/boostorg/unordered/pull/176  

Restores `boost::unordered_flat_[map|set]`'s allocator awareness by restricting `std::memcpy`-based element copy to `std::allocator` and allocators without a suitable  `construct` memfun.

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-12-12 23:14:11 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#issuecomment-1347476694  

Why are the warning restore pragmas not in reverse order of the suppression ones?

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2022-12-12 23:16:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/176#pullrequestreview-1214475142  

📁 test/objects/test.hpp

```diff
 238 |       case 2:
 239 |-         result = x * 7;
 239 |+         result = static_cast<int>(static_cast<boost::long_long_type>(x) * 7);
```

> Username: pdimov  
> Created_at: 2022-12-12 23:16:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#discussion_r1046484368  

Is this necessary?

> Username: cmazakas  
> Created_at: 2022-12-12 23:34:36 UTC  
> Updated_at: 2022-12-12 23:34:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#discussion_r1046494194  

That is a good question. Technically, this one didn't trigger ubsan's overflow checks.  
  
Currently, only `x * 256` did but I figured that if I was doing one, I'd also do the other just to be safe.  
  
The test suite seems to pass fine in the case of `x * 7` so if you'd like me to revert this change, it's no problem.

> Username: pdimov  
> Created_at: 2022-12-12 23:43:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#discussion_r1046499840  

I remember that when I initially fixed the ubsan failures, these two did not trigger. Does a newly added test cause an overflow here?

> Username: pdimov  
> Created_at: 2022-12-12 23:51:43 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#discussion_r1046523789  

Either way, this should probably be changed in the same way as the two previous functions, for consistency.


---

## Review 3 [Commented]

> Username: glenfe  
> Created_at: 2022-12-13 01:02:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/176#pullrequestreview-1214579079  

📁 include/boost/unordered/detail/foa.hpp

```diff
1042 |+ 
1043 |+ template<typename Allocator,typename Ptr,typename... Args>
1044 |+ struct alloc_has_construct
```

> Username: glenfe  
> Created_at: 2022-12-13 01:02:50 UTC  
> Updated_at: 2022-12-13 01:03:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#discussion_r1046562661  

Maybe the better thing to do is we instead enhance `boost::allocator_construct_n()` to call `memcpy()` when `alloc_has_construct` is `false` and `T` is trivially copyable.   
  
(So that this code could just call `allocator_construct_n` instead of copying `alloc_has_construct` etc.)

> Username: cmazakas  
> Created_at: 2022-12-13 04:50:34 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#discussion_r1046657371  

@joaquintides Personally, I'd be interested in seeing this. How do you feel? I don't mind holding back the PR to wait for an implementation.

> Username: glenfe  
> Created_at: 2022-12-13 04:55:17 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#discussion_r1046659441  

No need to hold back the PR. :) Can always refactor later.

> Username: pdimov  
> Created_at: 2022-12-13 06:16:52 UTC  
> Updated_at: 2022-12-13 06:16:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#discussion_r1046698524  

I'm not sure this will help here, because the bucket array can have "holes" in it, unconstructed elements, but is still copied with a single memcpy. (Not 100% positive though.)

> Username: pdimov  
> Created_at: 2022-12-13 06:17:48 UTC  
> Updated_at: 2022-12-13 06:17:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#discussion_r1046699069  

Maybe just expose `allocator_has_construct` as a supported, user-facing, primitive.

> Username: joaquintides  
> Created_at: 2022-12-13 08:15:27 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#discussion_r1046782010  

It's as Peter says, the `std::memcpy` call goes through an array with uninitialized portions. Having `allocator_has_construct` exposed would save us some some code duplication.


---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-12-13 05:35:22 UTC  
> Updated_at: 2022-12-13 17:37:54 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#issuecomment-1347773827  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/176?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#176](https://codecov.io/gh/boostorg/unordered/pull/176?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (200c0c6) into [develop](https://codecov.io/gh/boostorg/unordered/commit/6be2bf89b6aa1b05582ae0be4da08f0efefa1da1?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6be2bf8) will **increase** coverage by `0.04%`.  
> The diff coverage is `99.23%`.  
  
> :exclamation: Current head 200c0c6 differs from pull request most recent head 91eddba. Consider uploading reports for the commit 91eddba to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/176/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/176?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #176      +/-   ##  
===========================================  
+ Coverage    97.68%   97.73%   +0.04%       
===========================================  
  Files           83       83                
  Lines        12073    12195     +122       
===========================================  
+ Hits         11794    11919     +125       
+ Misses         279      276       -3       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/176?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/foa.hpp](https://codecov.io/gh/boostorg/unordered/pull/176/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS5ocHA=) | `98.60% <ø> (-0.02%)` | :arrow_down: |  
| [test/objects/test.hpp](https://codecov.io/gh/boostorg/unordered/pull/176/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9vYmplY3RzL3Rlc3QuaHBw) | `95.75% <50.00%> (+1.41%)` | :arrow_up: |  
| [test/unordered/copy\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/176/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvY29weV90ZXN0cy5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/176?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/176?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6be2bf8...91eddba](https://codecov.io/gh/boostorg/unordered/pull/176?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 5

> Username: joaquintides  
> Created_at: 2022-12-13 08:13:41 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#issuecomment-1347905713  

> Why are the warning restore pragmas not in reverse order of the suppression ones?  
  
I opted for having those pragmas be mutually exclusive (note the `#elif`s), hence restore order can't be reverse (some branches are true simultaneously).

---

## Review 6 [Commented]

> Username: glenfe  
> Created_at: 2022-12-13 12:53:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/176#pullrequestreview-1215411333  

📁 include/boost/unordered/detail/foa.hpp

```diff
1603 | #endif
1604 |+         &&(
1605 |+           is_std_allocator<Allocator>::value||
```

> Username: glenfe  
> Created_at: 2022-12-13 12:53:40 UTC  
> Updated_at: 2022-12-13 12:53:41 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#discussion_r1047118702  

What about users who specialize `std::allocator`?

> Username: joaquintides  
> Created_at: 2022-12-13 13:17:41 UTC  
> Updated_at: 2022-12-13 13:17:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#discussion_r1047143139  

Well, I guess they shouldn't :-) FWIW, stdlib implementors also assume that `std::allocator` does not have a fancy `construct` regardless of specializations:  
  
https://github.com/llvm-mirror/libcxx/blob/master/include/memory#L1670-L1701  
  
The way I see it, `std::memcpy` is too good not to use for the sake of corner cases such as you mention, which are in any case better served by proper user-defined allocators.

> Username: joaquintides  
> Created_at: 2022-12-13 13:31:57 UTC  
> Updated_at: 2022-12-13 13:33:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/176#discussion_r1047160068  

libstdc++ also assumes `std::allocator` is not to be user-specialized:  
https://github.com/gcc-mirror/gcc/blob/master/libstdc%2B%2B-v3/include/bits/stl_uninitialized.h#L334-L375


---
