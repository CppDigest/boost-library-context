# #144 Point iterator (change of file/dir layout and implementation design) [Merged]

> Username: mkaravel  
> Created at: 2014-09-30 12:10:47 UTC  
> Updated at: 2014-10-01 06:39:07 UTC  
> Merged at: 2014-09-30 12:13:52 UTC  
> Closed at: 2014-09-30 12:13:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/144  

In this PR:  
- I update the file/directory layout and implementation design for the point iterator (the `boost::geometry::detail::point_iterator` namespace is introduced and used).  
- The methods and contructors for the `point_iterator<>` and `point_reverse_iterator<>` classes are now declared as inline.  
- The `segment_iterator<>` implementation is updated following the changes above.

---
