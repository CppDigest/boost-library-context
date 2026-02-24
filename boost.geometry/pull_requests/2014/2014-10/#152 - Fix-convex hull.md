# #152 Fix/convex hull [Merged]

> Username: awulkiew  
> Created at: 2014-10-01 16:51:44 UTC  
> Updated at: 2014-10-31 13:41:15 UTC  
> Merged at: 2014-10-18 11:38:16 UTC  
> Closed at: 2014-10-18 11:38:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/152  

This PR fixes the number of Points outputted for open OutputGeometry when the output is valid Polygon (https://svn.boost.org/trac/boost/ticket/10562).  
  
Unfortunately the ConvexHullStrategy Concept was modified in the process. The Strategy must somehow know not only about the order but also about the closure of the Output. For now I just added additional run-time parameter to the `Strategy`'s `result()` method because there was already `clockwise` parameter used the same way.  
  
Since the Concept was changed maybe we could go further and modify it even more. E.g. why the algorithm is divided into the `apply()` and `result()` methods, plus some external state? Wouldn't it be more clear if just one method `apply()` was required?  
  
I'm also wondering, wouldn't it be better to get rid of the `convex_hull_insert()` function which is not used anywhere internally nor officially released?  
  
This would make simple another thing which could be done. The `OutputGeometry` could be directly passed into the `strategy::apply()`. Then the strategy could just internally check the order and closure. Keeping the support for non-Geometry parameters like output iterators could still be possible. This however complicates the Strategy as it moves the closure/order logic from the algorithm into the Strategy.  
  
I'm open to other suggestions.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-10-01 20:41:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/152#issuecomment-57534906  

The last commit fixes the last issue mentioned in the ticket. A "proper" number of Points is outputted also for edge cases when an invalid, 0-area Ring is generated, e.g. for closed output:  
  
```  
MULTIPOINT(0 0) -> POLYGON((0 0,0 0,0 0,0 0))  
MULTIPOINT(0 0, 1 1) -> POLYGON((0 0,1 1,0 0,0 0))  
```  
  
However there is one additional issue I'd like to address. AFAIU it's possible to pass a Linestring as an output Geometry. In this case it's treated as a closed Ring (bg::closure<> is set to closed for Linestring), which is ok in general I guess since a LinearRing is a Linestring with the last Point duplicated. But for the edge case of a Point-like or Segment-like output we could generate 2-Point Linestring. FYI, PostGIS returns POINT or LINESTRING in such cases.  
A few options how to achieve this:  
1. The algorithm could be dispatched with the OutputGeometry  
2. The Strategy could know the type of OutputGeometry, not only the OutputIterator  
3. There could be some simple condition at the end of the convex_hull() function, e.g. (typename keyword ommited for simplicity):  
  
```  
if ( boost::is_same<bg::tag<Output>::type, linestring_tag>::value  
  && boost::size(out) == 4  
  && bg::equals(bg::range::at(out, 0), bg::range::at(out, 2))  
{  
    bg::resize(out, 2);  
}  
```

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-10-18 10:46:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/152#issuecomment-59606933  

I'm fine with merging this. Thanks for the fix.  
W.r.t. the suggestions - we had some discussions earlier about Centroid strategies, they are apparently hard to define. Let's not discuss that in this PR but feel free to take this to the mailing list, for a later version.  
If this is merged, please add it also to the release notes.

---
