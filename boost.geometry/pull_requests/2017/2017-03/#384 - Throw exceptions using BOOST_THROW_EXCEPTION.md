# #384 Throw exceptions using BOOST_THROW_EXCEPTION. [Merged]

> Username: awulkiew  
> Created at: 2017-03-08 22:19:20 UTC  
> Updated at: 2017-09-06 12:29:57 UTC  
> Merged at: 2017-03-14 18:17:10 UTC  
> Closed at: 2017-03-14 18:17:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/384  

BOOST_THROW_EXCEPTION was used before in the library to throw exceptions but not in all places. This PR adds it consistently in all cases besides one place being `self_section_visitor` defined in `algorithms/detail/overlay/self_turn_points.hpp`. It's because in this case the exception should be removed entirely. This requires the changes in `partition` which will be proposed in another PR.  
  
Related ticket: https://svn.boost.org/trac/boost/ticket/12885

---

## Comment 1

> Username: awulkiew  
> Created_at: 2017-03-14 18:17:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/384#issuecomment-286512994  

Since noone has any objections and it's a simple fix I'm merging it.

---
