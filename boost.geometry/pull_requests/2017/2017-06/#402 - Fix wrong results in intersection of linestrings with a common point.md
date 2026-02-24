# #402 Fix wrong results in intersection of linestrings with a common point [Closed]

> Username: vissarion  
> Created at: 2017-06-27 10:21:52 UTC  
> Updated at: 2017-07-06 12:49:37 UTC  
> Closed at: 2017-07-03 15:38:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/402  

This PR proposes a fix for the bug related to ticket https://svn.boost.org/trac10/ticket/13057   
  
It is about producing wrong results when intersecting two linestrings with a common point.   
  
EDIT: working towards a more general solution that not only work for doubles

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2017-07-02 16:32:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/402#pullrequestreview-47554005  

📁 include/boost/geometry/algorithms/detail/relate/topology_check.hpp

```diff
 174 |             std::size_t count = boost::size(ls);
 175 | 
 176 |+             m_has_interior = false;
```

> Username: awulkiew  
> Created_at: 2017-07-02 16:32:25 UTC  
> Updated_at: 2017-07-03 15:32:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/402#discussion_r125187164  

This shouldn't be set here, It should be set outside the loop because otherwise a MultiLinestring won't have an interior if an empty Linestring is the last element of MultiLinestring.  
  
EDIT: And how it is now, for MultiLinestring with size 0 this member still won't be initialized.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2017-07-02 16:39:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/402#pullrequestreview-47554149  

📁 include/boost/geometry/algorithms/detail/disjoint/box_box.hpp

```diff
 126 |-              && b2_min_transl - constants::period() + diff2 < b1_min) // b2_max left of b1_min
 126 |+             // if the translation is too close then use the original point
 127 |+             if (math::abs(b2_max_transl - b2_max) < 100 * std::numeric_limits<calc_t>::epsilon())
```

> Username: awulkiew  
> Created_at: 2017-07-02 16:39:40 UTC  
> Updated_at: 2017-07-03 15:32:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/402#discussion_r125187307  

This may not work for non-fundamental types for which `epsilon()` is 0. I'd prefer some different solution probably checking additional conditions e.g. if `b2_max` and `b1_min` are lesser than 180 deg, etc.

> Username: vissarion  
> Created_at: 2017-07-03 10:46:28 UTC  
> Updated_at: 2017-07-03 15:32:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/402#discussion_r125261281  

AFAIU the unsigned difference `math::abs(b2_max_transl - b2_max)` takes values very close to `k*2*constants::period()` for k=0,1,2,... so the condition could be changed to  `math::abs(b2_max_transl - b2_max) < constants::period()`. Of course, checking conditions with  `b2_max` and `b1_min` is ok.

> Username: awulkiew  
> Created_at: 2017-07-03 12:19:39 UTC  
> Updated_at: 2017-07-03 15:32:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/402#discussion_r125275801  

How about changing `100 * epsilon()` to `half_period()`?


---

## Comment 3

> Username: awulkiew  
> Created_at: 2017-07-02 16:46:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/402#issuecomment-312502957  

Could you add an exact test of disjoint() for this edge-case? Either as a BG test case or, as a comment in this PR or in the ticket. I'd like to experiment with it.  
  
Please don't use vague commit messages such as "Simple workarounds for bugs", it doesn't mean anything.  
  
Also AFAIU these should rather be 2 separate commits because 2 different issues are fixed.

---

## Comment 4

> Username: vissarion  
> Created_at: 2017-07-03 15:38:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/402#issuecomment-312677853  

Closing this PR because of many conflicts and create a new one with the desired fix.

---
