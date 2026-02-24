# #1402 Add geometry polyhedral surface [Merged]

> Username: vissarion  
> Created at: 2025-04-30 10:39:22 UTC  
> Updated at: 2025-07-16 15:54:12 UTC  
> Merged at: 2025-05-12 09:08:51 UTC  
> Closed at: 2025-05-12 09:08:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402  

This PR is based on https://github.com/boostorg/geometry/pull/789 and adds a 3D geometry type for polyhedral surfaces according to OGC standard.   
  
The main class, concepts, i/o and basic traits are added.  
  
Comparing to https://github.com/boostorg/geometry/pull/789 it refines the code (since that was an old PR and the library evolved since then) and also defines the polyhedral surface as a set of polygons (instead of rings) as described in the standard.   
  
Finally, there are docs and unit tests.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-02 05:51:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1402#pullrequestreview-2811220546  

📁 example/08_polyhedralsurface_example.cpp

```diff
  24 |+     polyhedral_t polyhedral0;
  25 |+ 
  26 |+     // creating a polyhderal surface (cube) using standard initiallized list
```

> Username: barendgehrels  
> Created_at: 2025-05-02 05:50:59 UTC  
> Updated_at: 2025-05-02 05:51:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071129936  

Typos ->  `polyhedral` and `initialized` (and `Create` and `using a`)


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-02 05:51:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1402#pullrequestreview-2811220965  

📁 example/08_polyhedralsurface_example.cpp

```diff
  32 |+                                 {{{1, 1, 1}, {1, 1, 0}, {0, 1, 0}, {0, 1, 1}, {1, 1, 1}}}};
  33 |+ 
  34 |+     // creating a polyhderal surface (triangular pyramid) using append
```

> Username: barendgehrels  
> Created_at: 2025-05-02 05:51:26 UTC  
> Updated_at: 2025-05-02 05:51:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071130230  

The typo is repeated several times, could you search/replace?


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-02 05:52:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1402#pullrequestreview-2811221760  

📁 example/08_polyhedralsurface_example.cpp

```diff
  29 |+                                 {{{0, 0, 0}, {1, 0, 0}, {1, 0, 1}, {0, 0, 1}, {0, 0, 0}}},\
  30 |+                                 {{{1, 1, 1}, {1, 0, 1}, {0, 0, 1}, {0, 1, 1}, {1, 1, 1}}},\
  31 |+                                 {{{1, 1, 1}, {1, 0, 1}, {1, 0, 0}, {1, 1, 0}, {1, 1, 1}}},\
```

> Username: barendgehrels  
> Created_at: 2025-05-02 05:52:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071130791  

The backslashes are probably from the WKT variant.  
Does it even compile here?

> Username: vissarion  
> Created_at: 2025-05-02 08:57:58 UTC  
> Updated_at: 2025-05-02 08:57:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071313994  

It compiles but it shouldn't be here, so I removed it.


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-02 05:54:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1402#pullrequestreview-2811224407  

📁 example/08_polyhedralsurface_example.cpp

```diff
  60 |+     std::cout << bg::wkt(polyhedral0) << std::endl;
  61 |+     std::cout << bg::wkt(polyhedral1) << std::endl;
  62 |+     std::cout << bg::wkt(polyhedral2) << std::endl;
```

> Username: barendgehrels  
> Created_at: 2025-05-02 05:54:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071132459  

Great you picked it up again!  
* there should be credit to Sidharta in the copyright section  
* how sure are we that this all works and is according to the standards? Are there rules for ordering faces?   
* should we check at least one property (for example area or volume) and compare it with another library  
* or are there ways (other OS tools or so) to visualize it to see we didn't miss anything?

> Username: vissarion  
> Created_at: 2025-05-02 09:57:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071384960  

> Great you picked it up again!  
>   
>     * there should be credit to Sidharta in the copyright section  
  
I have already added as a commit coauthor, he hasn't added copyright notes in his original PR, but I agree that should be mentioned in copyrights too, so I added. @Siddharth-coder13 please let me know if you agree with those additions.   
  
>     * how sure are we that this all works and is according to the standards? Are there rules for ordering faces?  
  
We are not sure at this point, that check (consistent orientation of faces) could be part of validity checks. Another check should be related to dimension (assert that is 3). Does it make sense to add this in is_valid too? AFAIU this is similar to the overlapping polygon check for multi_polyogons (that check also applies for polyhedral surfaces).  
  
The standard also defines the following methods (that will be implemented in a subsequent PR):  
--- `NumPatches () : Integer` ⎯ Returns the number of including polygons  
--- `PatchN (N: Integer): Polygon` ⎯ Returns a polygon in this surface, the order is arbitrary.  
--- `BoundingPolygons (p: Polygon): MultiPolygon` ⎯ Returns the collection of polygons in this surface that  
bounds the given polygon “p” for any polygon “p” in the surface.  
--- `IsClosed (): Integer` - Returns 1 (True) if the polygon closes on itself, and thus has no boundary and  
encloses a solid  
  
>     * should we check at least one property (for example area or volume) and compare it with another library  
  
Sure, but I think that should be after implementing `is_valid` and the basic methods above.   
  
>     * or are there ways (other OS tools or so) to visualize it to see we didn't miss anything?  
  
there are a few visualization software (e.g. geomview) but I do not know of any that supports WKT for polyhedral surfaces.

> Username: vissarion  
> Created_at: 2025-05-02 12:54:08 UTC  
> Updated_at: 2025-05-02 12:54:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071571229  

EDIT: `NumPatches ()` and `PatchN (N: Integer)` are already implemented by calling `size()` and `[]`.


---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-02 05:55:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1402#pullrequestreview-2811226031  

📁 include/boost/geometry/geometries/concepts/polyhedral_surface_concept.hpp

```diff
  27 |+     using polygon_type = typename boost::range_value<Geometry>::type;
  28 |+ 
  29 |+ 	BOOST_CONCEPT_ASSERT( (concepts::Polygon<polygon_type>) );
```

> Username: barendgehrels  
> Created_at: 2025-05-02 05:55:58 UTC  
> Updated_at: 2025-05-02 05:55:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071133724  

Indent


---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-02 05:56:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1402#pullrequestreview-2811227132  

📁 include/boost/geometry/geometries/polyhedral_surface.hpp

```diff
  27 |+ 
  28 |+ /*!
  29 |+ \brief A Polyhedral Surface is a contiguous collection of polygons,
```

> Username: barendgehrels  
> Created_at: 2025-05-02 05:56:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071134535  

of 3 dimensional polygons?

> Username: vissarion  
> Created_at: 2025-05-02 08:59:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071315286  

Right, I edited it to "...collection of polygons in 3-dimensional space"


---

## Review 7 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-02 05:57:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1402#pullrequestreview-2811227790  

📁 include/boost/geometry/geometries/polyhedral_surface.hpp

```diff
  28 |+ /*!
  29 |+ \brief A Polyhedral Surface is a contiguous collection of polygons,
  30 |+     which share common boundary segments.
```

> Username: barendgehrels  
> Created_at: 2025-05-02 05:57:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071135006  

we don't check it. Suggestion:   
"The concepts and algorithms don't check if the boundary segments are common"

> Username: vissarion  
> Created_at: 2025-05-02 09:05:59 UTC  
> Updated_at: 2025-05-02 09:06:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071323227  

I also added that common boundary segments should be checked by is_valid()


---

## Review 8 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-02 05:58:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1402#pullrequestreview-2811228691  

📁 include/boost/geometry/io/wkt/read.hpp

```diff
 460 | 
 461 |+ template<typename PolyhedralSurface>
 462 |+ struct polyhderal_surface_parser
```

> Username: barendgehrels  
> Created_at: 2025-05-02 05:58:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071135621  

Typo also here


---

## Review 9 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-02 05:59:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1402#pullrequestreview-2811229664  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
 230 | 
 231 |+ template <typename Polyhedral_surface, typename PrefixPolicy>
 232 |+ struct wkt_polyhedral
```

> Username: barendgehrels  
> Created_at: 2025-05-02 05:59:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071136252  

we might add `_surface` and the template parameter `PolyhedralSurface`


---

## Review 10 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-02 06:00:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1402#pullrequestreview-2811230989  

📁 test/geometries/polyhedral_surface.cpp

```diff
  39 |+                                 {{{1, 1, 1}, {1, 0, 1}, {0, 0, 1}, {0, 1, 1}, {1, 1, 1}}},\
  40 |+                                 {{{1, 1, 1}, {1, 0, 1}, {1, 0, 0}, {1, 1, 0}, {1, 1, 1}}},\
  41 |+                                 {{{1, 1, 1}, {1, 1, 0}, {0, 1, 0}, {0, 1, 1}, {1, 1, 1}}}};
```

> Username: barendgehrels  
> Created_at: 2025-05-02 06:00:29 UTC  
> Updated_at: 2025-05-02 06:00:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071137190  

Same comments as for the example.  
I think this unit test is fine, testing 3 use cases.  
But the example does not need to be an exact repetition and one example is enough.

> Username: vissarion  
> Created_at: 2025-05-02 09:10:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071329041  

Thanks, I removed the `example/08_polyhedralsurface_example.cpp` and kept the one in the docs.


---

## Comment 11

> Username: vissarion  
> Created_at: 2025-05-02 09:59:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#issuecomment-2846836714  

Thanks @barendgehrels for the quick review! I've addressed all your comments and replied to questions.

---

## Review 12 [Commented]

> Username: tinko92  
> Created_at: 2025-05-02 15:01:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1402#pullrequestreview-2812202524  

📁 doc/concept/polyhedral_surface.qbk

```diff
  21 |+ 
  22 |+ * There must be a specialization of the metafunction `traits::tag`, defining `polyhedral_surface_tag` as type
  23 |+ * It must behave like a Boost.Range Random Access Range
```

> Username: tinko92  
> Created_at: 2025-05-02 14:45:22 UTC  
> Updated_at: 2025-05-02 15:01:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071722321  

Both ranges and concepts have been standardized in C++20, which BG does not yet depend on, after the definition of the original BG concepts which have existing users. For new BG concepts like this one, which do not have legacy usage that they need to be compatible to, should we consider documenting the requirement as behaving like C++ standard ranges instead?

> Username: barendgehrels  
> Created_at: 2025-05-02 18:27:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071996132  

I'm not sure... we cannot yet make it dependent on C++20 yet...  
Also it would be weird to let some concepts depend on the std-ranges/concepts, and others not...

> Username: tinko92  
> Created_at: 2025-05-03 03:32:38 UTC  
> Updated_at: 2025-05-03 03:32:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2072305248  

> we cannot yet make it dependent on C++20 yet...  
  
Yes. To be clear, my thought was to document it based on the STL concept, not actually bumping the version requirement of the library. The behaviour could be used without depending on the actual C++ version in which it became part of the STL. The rationale is avoiding a future breaking change in this BG concept, if BG moves to a newer standard eventually.  
  
But I understand the point about consistency with existing BG concepts.

> Username: vissarion  
> Created_at: 2025-05-05 08:21:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2073033739  

It is a good point @tinko92 but I think we should do it once for all concepts.

---

📁 doc/concept/polyhedral_surface.qbk

```diff
  15 |+ [heading Description]
  16 |+ [concept PolyhedralSurface..polyhedral surface]
  17 |+ 
```

> Username: tinko92  
> Created_at: 2025-05-02 14:50:16 UTC  
> Updated_at: 2025-05-02 15:01:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071729158  

There seems to be text and a reference to the corresponding OGC simple feature missing here.


📁 include/boost/geometry/algorithms/clear.hpp

```diff
  73 | };
  74 | 
  75 |+ template <typename Polyhedral_surface>
```

> Username: tinko92  
> Created_at: 2025-05-02 14:57:35 UTC  
> Updated_at: 2025-05-02 15:01:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071739786  

Shoulds this be PolyhedralSurface (like MultiPoint)?


📁 include/boost/geometry/io/wkt/write.hpp

```diff
 421 | {};
 422 | 
 423 |+ template <typename Polyhedral_surface>
```

> Username: tinko92  
> Created_at: 2025-05-02 15:00:00 UTC  
> Updated_at: 2025-05-02 15:01:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2071743184  

Like previous comment, should this be PolyhedralSurface?


---

## Review 13 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-02 18:31:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1402#pullrequestreview-2812660685  

📁 doc/src/examples/geometries/polyhedral_surface.cpp

```diff
   2 |+ // QuickBook Example
   3 |+ 
   4 |+ // Copyright (c) 2025 Siddharth Kumar, Roorkee, India.
```

> Username: barendgehrels  
> Created_at: 2025-05-02 18:31:51 UTC  
> Updated_at: 2025-05-02 18:31:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2072000486  

👍 thanks - and cool that you co-authored, hadn't spotted that indeed.

> Username: Siddharth-coder13  
> Created_at: 2025-05-02 18:41:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2072011218  

Thanks @vissarion for mentioning me in the copyrights.


---

## Review 14 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-02 18:48:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1402#pullrequestreview-2812691465  

📁 doc/src/examples/geometries/polyhedral_surface.cpp

```diff
  33 |+                                 {{{1, 1, 1}, {1, 0, 1}, {0, 0, 1}, {0, 1, 1}, {1, 1, 1}}},
  34 |+                                 {{{1, 1, 1}, {1, 0, 1}, {1, 0, 0}, {1, 1, 0}, {1, 1, 1}}},
  35 |+                                 {{{1, 1, 1}, {1, 1, 0}, {0, 1, 0}, {0, 1, 1}, {1, 1, 1}}}}; /*< Creating a polyhedral surface (cube) using standard initialized list >*/
```

> Username: barendgehrels  
> Created_at: 2025-05-02 18:48:49 UTC  
> Updated_at: 2025-05-02 18:48:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2072018596  

I'm not experienced in this subject. I had to parse it mentally and it is a cube. Then I noticed it is written there - can that comment go first?  
  
Same below - it's nice to have a pyramid, which is kind of realistic. But I think the example is not correct. I would expect at least a point in the center (2.5, 2.5) which is not there. Or it is not a pyramid. Didn't parse the rest...  
  
Maybe a (squarish) pyramid would be a more realistic example. It should have 5 faces, the floor face having 4 points, the other faces having 3 points, if I'm correct. Or the closure points should be added in all faces. The 4 other faces should have one common point in the center.  
  
And then there should a picture of it (somehow, for example in geomview, constructed in another way then).  
  
Writing this gives me also the questions:  
* should the surfaces be closed? Will it follow the polygon rules?  
* should they be clockwise? Will it follow the polygon rules?

> Username: vissarion  
> Created_at: 2025-05-05 09:14:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2073110209  

[Here](https://trinket.io/python3/cc089613aaa6) there is a python script that can be used to visualize POLYHEDRALSURFACE WKTs.  
  
First, this is the triangular pyramid from the example  
POLYHEDRALSURFACE(((0 0 0,5 0 0,0 5 0,0 0 0)),((0 0 0,0 5 0,0 0 5,0 0 0)),((0 0 0,5 0 0,0 0 5,0 0 0)),((5 0 0,0 5 0,0 0 5,5 0 0)))  
![Screenshot from 2025-05-05 11-44-12](https://github.com/user-attachments/assets/a90dbade-0851-4eb8-a461-1cf90cbc18a5)  
(I added that picture in the docs)  
  
Polyhedral surfaces is not necessarily closed as described in the standard.   
  
> If each such LineString is the boundary of exactly 2 Polygon patches, then the PolyhedralSurface is a simple,  
closed polyhedron and is topologically isomorphic to the surface of a sphere.  
  
Thus the cube and the tringular pyrimid examples are polyhedra but the following polyhedral surface is not.   
  
POLYHEDRALSURFACE(((0 0 0,0 1 0,1 1 0,1 0 0,0 0 0)),((0 0 0,0 1 0,0 1 1,0 0 1,0 0 0)),((0 0 0,1 0 0,1 0 1,0 0 1,0 0 0)))  
![open_polyhedral_surface2](https://github.com/user-attachments/assets/53bd8f5a-b50b-4c05-b05a-ab54401403e8)  
  
Regarding the question on ordering (clockwise or counterclockwise), the polygons that describe the faces of the polyhedral surface will have some orientation (we mention that there should be a consistent orientation among polygons of the surface). The surface itself if closed then (coping from the standard) has some orientation:  
  
>In this case, the “top” of the surface will either point inward or outward of the enclosed finite solid.  
If outward, the surface is the exterior boundary of the enclosed surface. If inward, the surface is the interior of the  
infinite complement of the enclosed solid. A Ball with some number of voids (holes) inside can thus be presented  
as one exterior boundary shell, and some number in interior boundary shells.

> Username: barendgehrels  
> Created_at: 2025-05-06 06:02:03 UTC  
> Updated_at: 2025-05-06 06:02:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1402#discussion_r2074778907  

👍 thanks a lot for adding the pictures, and to the doc, and for your explanations!


---

## Review 15 [Approved]

> Username: barendgehrels  
> Created_at: 2025-05-06 06:04:32 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1402#pullrequestreview-2816954960  

Cool.  
  
Small thing, can we squash and rebase (no merge commit)? Especially commits like `fix typo` we should avoid in the branch, I think.

---
