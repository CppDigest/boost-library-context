# #163 Fix/disjoint [Merged]

> Username: awulkiew  
> Created at: 2014-10-18 01:16:15 UTC  
> Updated at: 2014-10-31 13:41:30 UTC  
> Merged at: 2014-10-18 11:28:02 UTC  
> Closed at: 2014-10-18 11:28:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/163  

PR fixing Areal/Areal version by replacing `point_on_surface()` with `point_on_border()` and `within()` with `covered_by()` for backward compatibility (https://svn.boost.org/trac/boost/ticket/8310).  
  
Related to https://github.com/boostorg/geometry/pull/159 and https://github.com/boostorg/geometry/pull/162.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-10-18 07:07:07 UTC  
> Updated_at: 2014-10-18 10:07:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/163#discussion_r19052316  

Why not combine the two if statements into one?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-10-18 07:07:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/163#issuecomment-59601571  

The comment in `general_areal` before calling `rings_containing` still mentions "point on surface".  
This needs to be changed.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-10-18 07:09:06 UTC  
> Updated_at: 2014-10-18 10:07:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/163#discussion_r19052325  

Or even:  
  
```  
has_within = geometry::point_on_border(pt, range) && geometry::covered_by(pt, m_geometry);  
```

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-10-18 07:53:48 UTC  
> Updated_at: 2014-10-18 10:07:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/163#discussion_r19052461  

Actually writing:  
  
```  
has_within = geometry::point_on_border(pt, range) && geometry::covered_by(pt, m_geometry);  
```  
  
is obviously wrong.  
What I wanted to suggest is:  
  
```  
has_within ||= (geometry::point_on_border(pt, range) && geometry::covered_by(pt, m_geometry));  
```  
  
This way you avoid any geometric computations (`point_on_border` and `covered_by`) ~~is~~ if  `has_within` has already been set to `true`.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2014-10-18 09:44:48 UTC  
> Updated_at: 2014-10-18 10:07:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/163#discussion_r19052805  

Yes, `has_within` should be checked and calculation done only if disjoint wasn't detected before. That's not all, the function should return right away when it's 1st time set to true. This was possible also in the old version.  
  
With that said, dispathing this algorithm for MultiPolygon doesn't seem to be that bad idea. An alternative would be to have `for_each_range_if()`. But for now I'll just apply your suggestion.  
  
Btw, there is no `||=` operator, however undoubtedly it'd be useful :)

---

## Comment 6

> Username: mkaravel  
> Created_at: 2014-10-18 09:51:01 UTC  
> Updated_at: 2014-10-18 10:07:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/163#discussion_r19052821  

I agree that `for_each_range_if()` would be a useful thing to have. Also `for_each_point()` and `for_each_segment()` could use an `..._if()` analogue.  
But for now and given the release schedule, this is not feasible.  
  
About `||=`: by bad. You get the point though.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2014-10-18 10:49:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/163#issuecomment-59607013  

I'm fine with merging this.  
Are there already tests for multi_polygon with the described problem?  
A WKT exists in the difference test (multi_no_ip)

---

## Comment 8

> Username: awulkiew  
> Created_at: 2014-10-18 11:27:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/163#issuecomment-59608753  

Ok, so everything's fine. With https://github.com/boostorg/geometry/pull/129 we're waiting for 1.58 we musn't consider conflicts between PRs. Merging.

---
