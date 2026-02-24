# #632 Fix/buffer onesided [Merged]

> Username: barendgehrels  
> Created at: 2019-10-30 13:59:50 UTC  
> Updated at: 2019-10-30 21:38:18 UTC  
> Merged at: 2019-10-30 21:37:02 UTC  
> Closed at: 2019-10-30 21:37:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/632  

This PR (not related to rescaling / robustness, but found it when working at it) fixes two related things:  
  
- the end-cap was generated wrongly for one-sided buffers  
- turn points were classified wrongly for one-sided buffers

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2019-10-30 14:00:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/632#pullrequestreview-309222625  

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 550 |-             if (it->count_on_original_boundary > 0)
 551 |-             {
 552 |-                 it->location = inside_buffer;
```

> Username: barendgehrels  
> Created_at: 2019-10-30 14:00:52 UTC  
> Updated_at: 2019-10-30 14:00:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/632#discussion_r340630682  

Because this counter is now set in a different way, this condition could go


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2019-10-30 15:48:40 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/632#pullrequestreview-309305177  

Looks fine, thanks!

📁 doc/release_notes.qbk

```diff
  26 |+ [*Improvements]
  27 |+ 
  28 |+ * Various improvements related to robustness of set and relational operation.
```

> Username: vissarion  
> Created_at: 2019-10-30 15:37:12 UTC  
> Updated_at: 2019-10-30 15:48:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/632#discussion_r340693429  

it would be useful to have here the PR links


📁 test/algorithms/buffer/buffer_linestring.cpp

```diff
 186 |-     test_one<linestring, polygon>("overlapping_asym_150_100", overlapping, join_round, end_flat, 62.514, 1.5, ut_settings(), 1.0);
 187 |-     test_one<linestring, polygon>("overlapping_asym_150_100", overlapping, join_miter, end_flat, 64.984, 1.5, ut_settings(), 1.0);
 184 |+     test_one<linestring, polygon>("overlapping_asym_150_010", overlapping, join_round, end_flat, 48.308, 1.5, settings, 0.25);
```

> Username: vissarion  
> Created_at: 2019-10-30 15:43:20 UTC  
> Updated_at: 2019-10-30 15:48:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/632#discussion_r340697416  

is it ok to have that long lines?

---

📁 test/algorithms/buffer/buffer_linestring.cpp

```diff
 346 |+ 
 347 |     // Left
 330 |-     test_one<linestring, polygon>("mysql_25662426a_1", mysql_25662426a, join_round32, end_round32, 54.9018, 1.0, ut_settings(), 0.0);
```

> Username: vissarion  
> Created_at: 2019-10-30 15:48:24 UTC  
> Updated_at: 2019-10-30 15:48:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/632#discussion_r340700739  

are those tests still failing or the new ones are also covering those?


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2019-10-30 21:38:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/632#issuecomment-548123767  

Thanks for the quick approvals.  
  
W.r.t. @vissarion 's remark about release notes, that line is copied from earlier releases where the same is done, it is a continuous operation - we might add PR's but I doubt if it is necessary.

---
