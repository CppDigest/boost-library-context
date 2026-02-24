# #75 Feature/is simple [Merged]

> Username: mkaravel  
> Created at: 2014-06-24 11:10:29 UTC  
> Updated at: 2014-07-01 15:27:01 UTC  
> Merged at: 2014-07-01 15:27:01 UTC  
> Closed at: 2014-07-01 15:27:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/75  

First complement implementation of is_valid/is_simple for all geometries.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-06-24 14:17:29 UTC  
> Updated_at: 2014-07-01 14:51:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/75#discussion_r14131195  

Is this macro necessary?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-06-24 14:23:45 UTC  
> Updated_at: 2014-07-01 14:51:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/75#discussion_r14131630  

@awulkiew It is a left-over. I will remove it before the merge.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-06-24 16:44:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/75#issuecomment-46996654  

@awulkiew Macro removed

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-06-27 10:38:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/75#issuecomment-47329971  

The last commit above, among other things, implements the optimization where the rings are tested for relative containment only when there are no turns associated with them.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2014-06-30 12:34:25 UTC  
> Updated_at: 2014-07-01 14:51:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/75#discussion_r14347342  

The code above bothers me. It sets mutable variables in const methods which probably shouldn't be done. Mutable variables has sense if semantically the method doesn't change the internals but this is not the case.  
And btw the names set_parent_id() and set_visited() would better reflect the behavior.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2014-06-30 12:43:14 UTC  
> Updated_at: 2014-07-01 14:51:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/75#discussion_r14347633  

Ok, it's probably because the std::set is used. The sorting is done using only the m_is_ip and m_turn_point. Instead of std::set, std::map should probably be used, and neighbor_iterator should be a mutable iterator. Then there would be no problems with const-correctness.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2014-06-30 12:53:27 UTC  
> Updated_at: 2014-07-01 14:51:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/75#discussion_r14348009  

@awulkiew I wasn't sure whether visited(bool) or set_visited(bool) (and similarly for parent_id) was preferable; I almost randomly chose the first. I will modify this and commit.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2014-06-30 13:21:58 UTC  
> Updated_at: 2014-07-01 14:51:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/75#discussion_r14349184  

const-correctness is more important. In BG get/set names are used quite frequently so I'm guessing that using them wouldn't be bad. My main concern was the use of a const method to modify an object. This would be even more noticeable if the method has the name starting with set.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2014-06-30 21:08:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/75#issuecomment-47587344  

@awulkiew Complement graph has been re-designed. There are no mutable fields any more.

---
