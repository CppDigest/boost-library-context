# #755 [fix] #750: misspelled config macros [Merged]

> Username: ayaankhan98  
> Created at: 2020-09-24 07:10:24 UTC  
> Updated at: 2020-10-16 14:26:37 UTC  
> Merged at: 2020-10-16 14:26:09 UTC  
> Closed at: 2020-10-16 14:26:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/755  

refer to #750

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2020-09-24 09:28:34 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/755#pullrequestreview-495385399  

Seems OK to me, the macro `BOOST_NO_INT64_T`indicates the absence of `int64_t` and `uint64_t` (as documented in https://www.boost.org/doc/libs/1_74_0/libs/config/doc/html/boost_config/cstdint.html) and the other two (`BOOST_NO_INT64`, `BOOST_HAS_INT64_T`) are not in boost anymore.

---

## Comment 2

> Username: ayaankhan98  
> Created_at: 2020-10-16 01:52:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/755#issuecomment-709682122  

@vissarion can you merge this?

---

## Comment 3

> Username: vissarion  
> Created_at: 2020-10-16 06:54:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/755#issuecomment-709857338  

> @vissarion can you merge this?  
  
It's a good practice to have at least 2 reviews for each merged PR. @awulkiew @barendgehrels @mloskot any feedback on this PR?

---

## Comment 4

> Username: awulkiew  
> Created_at: 2020-10-16 14:24:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/755#issuecomment-710079252  

Yes, I'm ok with merging it. Thanks @ayaankhan98.  
  
Note that all of such checks could be removed because we're moving to the newer standard and 64b int is guaranteed to be available. But let's not do that for now and leave it as it is.

---
