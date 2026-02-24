# #321 Enlarge nodes boxes in the rtree WRT epsilon when non-Box values are indexed. [Merged]

> Username: awulkiew  
> Created at: 2015-08-23 14:06:02 UTC  
> Updated at: 2015-10-09 13:56:14 UTC  
> Merged at: 2015-10-09 13:54:03 UTC  
> Closed at: 2015-10-09 13:54:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/321  

This PR is related with the PR https://github.com/boostorg/geometry/pull/320 and contains commits from it. It should be merged after it if the change was desireable.  
  
In a similar way to `sectionalize()` bounding boxes of rtree nodes representing groups of Points or Segments are expanded WRT machine epsilon. There is no need to do it for Boxes because for nodes' Boxes and values' Boxes the spatial relations are checked the same way.  
  
Side note:  
It is the same for all bounding objects, the spatial relations should be checked similar, fast way. E.g. Nsphere from extensions probably shouldn't use the epsilon in relops as well as the Box. And relops taking any bounding objects shouldn't use epsilon. But if used as a bounding objects of some OGC geometries they should be enlarged the same way as Boxes. This means that in a general, we have the class of bounding objects handled similar way, for now officially only one - Box. It behaves in a different way than the other Geometries.  
  
But back to the subject. There is no difference in rtree performance WRT the state of the library from before PR https://github.com/boostorg/geometry/pull/318.  
  
WRT the state of the library from before PR https://github.com/boostorg/geometry/pull/318. The rtree spatial query is able to return all of the desired values. That is to give the same result as if the values were checked without the rtree.  
  
However the bounding boxes of the rtree are no longer "minimal" in a strict value sense (max/min coordinates) if the Indexables are Points or Segments.  
  
Another side note:  
This also brings a question: is this naiive, strict definition of "minimal" bounds correct in the case of Boost.Geometry? A minimum bounding box must represent a minimum space containing some objects. But in Boost.Geometry OGC geometries are handled WRT machine epsilon. This means for example that Points are not point-size objects but has some area and rectangular shape, lines has some width, etc. So maybe in our case a minimum bounding box indeed is the enlarged box not the strict one?

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-08-23 14:17:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/321#issuecomment-133849299  

Nobody yet complained about the way results of spatial queries or how bounding boxes are created in the rtree, so I'm wondering if this is indeed an issue that should be addressed.  
  
I'm also considering allowing the users to disable/enable this enlargement in the rtree somehow, e.g. with a macro definition.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-08-24 06:54:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/321#issuecomment-134063942  

Having a macro definition that allows to enable or disable the box expansion is something that could be potentially helpful. If it is not too much work I would go for having such a macro.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-10-08 17:15:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/321#issuecomment-146627856  

I added macro definition checks. By default the enlargement is disabled, so the rtree should work the same as before. The enlargement may be enabled if needed. I've choosen this path because no problems related to this issue were identified. In the future if we had any we could enable it by default.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-10-09 13:41:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/321#issuecomment-146874256  

Thank you for adding the macro.  
I am okay with merging.

---
