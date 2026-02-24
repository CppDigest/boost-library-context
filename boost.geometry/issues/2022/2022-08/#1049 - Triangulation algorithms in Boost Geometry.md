# #1049 - Triangulation algorithms in Boost Geometry [Closed]

> Username: phimuemue  
> Created at: 2022-08-04 09:25:51 UTC  
> Updated at: 2022-09-05 07:31:49 UTC  
> Closed at: 2022-08-05 18:07:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1049  

I'm using Boost Geometry, version 1.79, and I'd like to triangulate polygons (concretely: a multi-polygon, using a polygon type that is typically based on `int` or `double`, oriented counter-clockwise and open (not closed)).  
  
So far, for triangulation I resort to [the Boost Polygon Library](https://www.boost.org/doc/libs/1_79_0/libs/polygon/doc/index.htm), which has [`get_trapezoids`](https://www.boost.org/doc/libs/1_79_0/libs/polygon/doc/gtl_polygon_set_concept.htm), but - according to the documentation - does not support floating point coordinate data types. (Which I confirmed: It runs into undefined behavior when used with certain `double` coordinates.)  
  
Ideally I'd get rid of this additional dependency (all my other needs are catered by Boost Geometry) and triangulate `double`-polygons with Boost Geometry. Did I miss any relevant algorithms from the documentation? If not, are there plans to incorporate triangulation into Boost Geometry?

---

## Comment 1

> Username: barendgehrels  
> Created at: 2022-08-05 18:07:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1049#issuecomment-1206715369  

There are no current plans. But the MapBox EarCut C++ library works very well, also in combination with Boost.Geometry  
https://github.com/mapbox/earcut.hpp

---

## Comment 2

> Username: vissarion  
> Created at: 2022-09-01 07:35:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1049#issuecomment-1233870369  

@phimuemue there is a prototype by @tinko92 implemented as part of a GSoC project in 2019. Here is the PR https://github.com/boostorg/geometry/pull/684 which looks stale but the code AFAIR can be used (at least I have used it successfully in the past).   
  
@tinko92 are you willing to finish that PR? Do you remember if that was ready for reviewing?

---

## Comment 3

> Username: tinko92  
> Created at: 2022-09-01 23:05:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/1049#issuecomment-1234875811  

@vissarion Yes, I am willing. I do remember that I thought it was ready for review back when I posted it as a PR, but with 2 more years of experience with BG and a better understanding of the library, I have a more critical view of the PR. I think it would not be so trivial to review and merge it because it introduces a new triangulation data structure which doesn't really have an analogue in the existing library; it is not really a geometry concept like the others IMHO, so it would probably be necessary to discuss how that should be designed to fit into the BG philosophy and I would probably rewrite large parts of it. It also has robustness issues that I didn't realize back then when I wrote it.  
  
I would also prioritize other things that I'd think would be useful for BG. I'm still looking into robustness issues within overlay. Wrt GSoC PRs, I think reviewing #618 would make sense before #684. I think #618 is in a better state and more straightforward to integrate into BG.

---

## Comment 4

> Username: awulkiew  
> Created at: 2022-09-04 11:19:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/1049#issuecomment-1236315143  

@vissarion AFAIU @tinko92 implemented Delaunay triangulation which is a different kind than the one that is requested here. It can't be used to triangulate a polygon, or am I missing something?

---

## Comment 5

> Username: tinko92  
> Created at: 2022-09-04 11:23:31 UTC  
> Updated at: 2022-09-04 11:33:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/1049#issuecomment-1236315831  

The implemented Delaunay Triangulation algorithm takes a range of points without any assumption/constraint of whether they form, e.g. a valid polygon and the triangulation will cover the convex hull of that point set with finite triangles. A constrained Delaunay Triangulation, which was not implemented, could respect an input polygon.  
  
Edit: Sorry for not mentioning this earlier, I forgot about that requirement in the opening post.

---

## Comment 6

> Username: vissarion  
> Created at: 2022-09-05 07:31:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/1049#issuecomment-1236639233  

Sorry for the confusion! My intention was to connect the similar issue of Delaunay triangulation implemented by @tinko92 not to state that that implementation resolves the current issue. What is requested here is a constrained triangulation CT (not necessary a Delaunay one). Indeed the implementation of https://github.com/boostorg/geometry/pull/684 cannot be used to triangulate a polygon (unless if that polygon is convex). But I think the triangulation data structure introduced in https://github.com/boostorg/geometry/pull/684 could be used in the future by a CT algorithm. Also there should be a possibility of implementing a post processing procedure that converts a Delaunay triangulation of the polygon vertex set to a constrained Delaunay triangulation i.e. a triangulation of the input polygon.
