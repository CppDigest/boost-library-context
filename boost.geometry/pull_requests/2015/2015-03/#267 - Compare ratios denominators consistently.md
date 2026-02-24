# #267 [strategies][cart_intersect] Compare ratios denominators consistently. [Merged]

> Username: awulkiew  
> Created at: 2015-03-12 22:33:23 UTC  
> Updated at: 2015-04-09 01:15:37 UTC  
> Merged at: 2015-03-22 13:41:41 UTC  
> Closed at: 2015-03-22 13:41:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/267  

Compare both ratios' potential denominators, corresponding to both  
segments. Furthermore, take into account the machine epsilon.  
  
Without this change an assertion `ratio.denominator() != 0` can fail in intersection_points.hpp line 62, in segments_intersection_points policy. And indeed it fails e.g. in get_turns_linear_linear.cpp unit test if compiled in MinGW with -O2.  
  
Furthermore, if compiled with -O2, the tests which "normally" was passing fails. Some of those cases are fixed if the denominators are compared WRT machine epsilon.  
  
However not all of them. AFAIU the reason is different and related to the way how the determinant is calculated. See this PR: https://github.com/boostorg/geometry/pull/259 and this ticket: https://svn.boost.org/trac/boost/ticket/8379.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-03-22 10:09:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/267#issuecomment-84579678  

I'm OK with this, thanks

---
