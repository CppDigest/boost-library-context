# #716 - Ring iterators required to return references to points, otherwise dangling references happen [Closed]

> Username: autc04  
> Created at: 2020-05-20 15:54:18 UTC  
> Updated at: 2020-12-01 23:19:18 UTC  
> Closed at: 2020-12-01 23:19:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/716  

According to my reading of the documentation, iterators for the vertices of an adapted ring type are allowed to return any type convertible to `T` from their `operator*()`.  
  
However, linear_segment_or_box.hpp, line 101 constructs a `referring_segment` from dereferenced iterators into a ring:  
  
```  
                range_segment rng_segment(*it0, *it1);  
```  
  
where `range_segment` is defined as:  
```  
 typedef typename geometry::model::referring_segment  
            <  
                point_type const  
            > range_segment;  
```  
  
If the return value of `*it0` and `*it1` is anything but a `point_type&` or a `const point_type&`, this silently constructs a dangling reference to a temporary, leading to hard-to-debug failures.  
  
  
The documentation for the ring concept refers to the Boost.Range Random Access Range concept, which says that the iterators must be [Random Access Traversal Iteratos](https://www.boost.org/doc/libs/1_72_0/libs/iterator/doc/new-iter-concepts.html#random-access-traversal-iterators-lib-random-access-traversal-iterators).  There, the only requirement on the return type of `*a` is that it is   
"Convertible to T".  
  
Requiring the return type to be (const) `point_type&`, besides being currently undocumented, precludes writing adapters for types where the points are not stored explicitly.

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-05-20 21:56:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/716#issuecomment-631748822  

Yes, I think it's a bug in the library. Thanks for reporting!

---

## Comment 2

> Username: awulkiew  
> Created at: 2020-08-02 22:47:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/716#issuecomment-667734450  

@autc04 I have a patch for this issue:  
https://github.com/boostorg/geometry/pull/740  
Could you check if it works for you?
