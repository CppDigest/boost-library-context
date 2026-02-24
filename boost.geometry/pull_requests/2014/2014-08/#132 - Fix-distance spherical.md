# #132 Fix/distance spherical [Merged]

> Username: awulkiew  
> Created at: 2014-08-29 21:58:17 UTC  
> Updated at: 2014-10-31 13:41:42 UTC  
> Merged at: 2014-10-18 11:40:25 UTC  
> Closed at: 2014-10-18 11:40:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/132  

This PR fixes the issue from https://svn.boost.org/trac/boost/ticket/10421  
  
The first commit disables the generation of cartesian default strategy for distance(Point/Box) and distance(Box/Box) for non-cartesian CS. The default strategy is retrieved taking into account the CS of Geometries like in all other cases.  
  
The second commit adds the distance(Point/Box) strategy for spherical CS. It should be optimized and tweaked (see the TODOs). It uses side_by_cross_track and distance_cross_track so calculates the same thing twice. For this as much reusable code as possible from those strategies should be extracted and reused in all of those strategies. For instance there are 2 implementations calculating the course(), returning different types. It should probably be unified into `bg::detail::course<CalculationAndReturnType>(Pt1, Pt2)`.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-08-30 21:11:56 UTC  
> Updated_at: 2014-09-01 12:40:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#issuecomment-53970679  

As a general remark regarding the algorithms working for spherical boxes, I noticed that the ways how various algorithms handles boxes are inconsistent:  
- expand(point) returns a box naiively expanded, i.e. box's min/max <1> coordinate (latitude) is set accordingly to the point's <1>. We could say that for <1> the box's edge is contained in the **small circle**.  
- disjoint/intersects(point, box) naiively checks the coordinates (same algorithm as for cartesian CS) which means that it uses **small circle** edges and assumes that always MIN <= MAX for longitudes which seems to be a reasonable requirement.  
- within/covered_by(point, box) uses spherical-side formula per edge, edges are defined by **great circles** for both longitudes and latitudes.  
- distance(point, box) (and point-segment/linestirng/polygon/etc.) added by this PR uses cross-track formula per edge, edges are also defined by **great circles**.  
  
The above leads to inconsistencies. The examples below uses points/boxes defined in `spherical_equatorial<degree>`. `intersects()` and `covered_by()` should give the same results and if they returned `true` the distance should be `0`:  
  
```  
box aabb(point(0, 50),(point(20, 70)));  
point pt(10, 50.1);  
bg::intersects(pt, aabb); // true - small circle  
bg::covered_by(pt, aabb); // false - great circle  
bg::distance(pt, aabb);   // 0.0057825 - great circle  
  
// box degenerated to segment  
box aabb(point(0, 50),(point(20, 50)));  
point pt(10, 50);  
bg::intersects(pt, aabb); // true - small circle  
bg::covered_by(pt, aabb); // false - great circle  
bg::distance(pt, aabb);   // 0.00752783 - great circle  
  
// box degenerated to Point  
box aabb(point(0, 0),(point(0, 0)));  
point pt(1, 1);  
bg::intersects(pt, aabb); // false  
bg::covered_by(pt, aabb); // true - probably error in the algorithm not taking into account edge cases  
bg::distance(pt, aabb);   // 0.0246821  
```  
  
We should develop a consistent (default) way of handlind Boxes across the whole library in the near future. Should all edges be defined by great circles like in the case of Polygons. Or should "vertical" edges be defined by great circles but "horizontal" edges by small circles so more or less reflect how lat/lon coordinates defines positions on a sphere, differently than e.g. Polygons' edges.  
  
The former probably means the changes in e.g. expand() and intersects() for e.g. Points to always take into account the great circles. The latter means that e.g. the envelope for a Polygon/Segment would have to be calculated differently since for Polygons/Segments great circles would always be used to define Segments which would be inconsistent with the "horizontal" AABB edges defined by small circles.  
  
EDIT:  
  
Another possibility, some kind of a hybrid approach. AABB edges could define a fragment of a surface covering all possible contained Geometries, so in general exactly what AABB should do. For simplicity let's consider AABB lying entirely in the northern hemisphere. In this case the lower/south "horizontal" edge could be defined by the **small circle** and the upper/north "horizontal" edge could be defined by the **great circle**. This kind of AABB could be created by naiive expanding of coordinates by Points or any Geometry. It could cover Polygons with edges defined by great circles as well as single Points.  
  
So e.g. for a 2 Points at the same latitude (or a "horizontal" Segment) the AABB would lie on a fragment of a sphere's surface defined/between a small-circle-lower-edge and great-circle-upper-edge. If we decided to use this model then such Box would probably always have an interior and therefore probably non-0 area, as far as it wasn't degenerated to a Point.  
  
EDIT2: This mixed approach could be unintuitive since the results for boxes could be different than the results for rings representing boxes, e.g.: `within(point, box) != within(point, box_as_ring)`.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-09-05 07:29:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#issuecomment-54593463  

@awulkiew I did not have time to go over this yet.  
Please wait a bit before merging.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2014-09-30 12:18:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#issuecomment-57304622  

I propose to merge this PR. We should work on the Box model and consistency but this affects all functions, not only distance(). This PR at least fixes distance(Pt, Box) in one of the possible ways.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-10-15 06:48:21 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18877061  

Is `d` here `dlon`? If yes, the comment should be more precise.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-10-15 06:51:17 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18877134  

This line is too long. All lines should be up to 80 characters (at least in the actual code part, because I do not see how we could enforce this rule for `#include` commands and file guarding macros).

---

## Comment 6

> Username: mkaravel  
> Created_at: 2014-10-15 06:52:08 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18877154  

This line seems too long also. Same for a few more lines below.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2014-10-15 06:53:18 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18877179  

Why do we need the box-point version? Isn't the point-box version of `default_strategy` enough?

---

## Comment 8

> Username: mkaravel  
> Created_at: 2014-10-15 06:56:56 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18877265  

In this PR you have not introduced a box-box strategy. In that respect does this change really offer something?

---

## Comment 9

> Username: mkaravel  
> Created_at: 2014-10-15 07:00:07 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18877344  

Not really relevant to this PR, but it gives me the opportunity to comment about it: I think that all math functions we use in BG should go through the `bg::math` namespace where we define them. I think this is important if we want to have proper support for user-defined number types.

---

## Comment 10

> Username: mkaravel  
> Created_at: 2014-10-15 07:03:25 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18877432  

It is possible that compilers optimize this, so whatever I write below may not matter.  
  
In a for-loop, I always use the pre-increment operator for the for-loop variable. The reason is that the post-increment operator would create a new object, which is not the case for the pre-increment operator.  
So I would write the for-loop as:  
  
```  
for (int i = 1; i < 5; ++i)  
```

---

## Comment 11

> Username: mkaravel  
> Created_at: 2014-10-15 07:03:59 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18877445  

I believe there is a missing space between `if` and `(`.

---

## Comment 12

> Username: mkaravel  
> Created_at: 2014-10-15 07:04:49 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18877467  

I also prefer to leave spaces after `(` and before `)`, like you have done above in `if ( sigXTD > 0 )`.

---

## Comment 13

> Username: mkaravel  
> Created_at: 2014-10-15 07:15:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#issuecomment-59166670  

Since there is a point-box strategy, I was expecting also a box-box strategy. What are your plans regarding the second? This is also related to the changes you did in `detail/distance/default_strategies.hpp` (see comment above).

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2014-10-15 07:59:23 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18879166  

That is for iterators.  
For int or std::size_t this really does not matter - I always write it as i++ just because this is still the most common used

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2014-10-15 08:00:47 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18879214  

We did this never before (inside the brackets). Indeed after if a space should be added

---

## Comment 16

> Username: barendgehrels  
> Created_at: 2014-10-15 08:04:03 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18879320  

This comment is taken literally from Williams site (the quotes indicate that).  
Adam, thanks for moving this to a generic file.

---

## Comment 17

> Username: mkaravel  
> Created_at: 2014-10-15 08:04:17 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18879328  

@awulkiew You may disregard this. Apparently, `bg::strategy::distance::services::default_strategy` does not do reverse dispatching, so this is actually needed. Not really by our own distance code, but rather for the advanced user who would want to specify the default strategy manually.  
  
I think the correct solution here is to somehow make `bg::strategy::distance::services::default_strategy` do its own reverse dispatching.

---

## Comment 18

> Username: barendgehrels  
> Created_at: 2014-10-15 08:06:42 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18879409  

Ah so I already created a copy.... Good you moved it.

---

## Comment 19

> Username: mkaravel  
> Created_at: 2014-10-15 08:07:49 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18879456  

So shouldn't we write in a non-quoted comment that `d` is `dlon`?

---

## Comment 20

> Username: mkaravel  
> Created_at: 2014-10-15 08:08:42 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18879482  

@barendgehrels You mean that we have no such coding rule, right?

---

## Comment 21

> Username: barendgehrels  
> Created_at: 2014-10-15 08:12:06 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18879602  

@mkaravel Looked it up, but yes we have indeed.   
https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers  
  
if (expr)  
Parentheses around expressions should not be pre/post-fixed with spaces.

---

## Comment 22

> Username: mkaravel  
> Created_at: 2014-10-15 08:15:55 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18879800  

Which means that line 112 should be changed then (and probably others).

---

## Comment 23

> Username: awulkiew  
> Created_at: 2014-10-15 10:24:59 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18885366  

It disables the cartesian default strategy for non-cartesian coordinate systems, which I assumed was enabled by mistake. I considered it as a bug. Do you think we should do something else with it? E.g. use cartesian version in 1.57 and fix it in 1.58?

---

## Comment 24

> Username: awulkiew  
> Created_at: 2014-10-15 14:08:49 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18895338  

Yes, this should be a library-wide change. And we should clean up the numeric_limits<> usage as well. But as you said, not in this PR.

---

## Comment 25

> Username: awulkiew  
> Created_at: 2014-10-15 14:21:18 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18896156  

Sure I may change it, however I found this particular guideline not a very good one because IMHO in case of a multi-line condition it's more readable this way:  
  
```  
if ( geometry::equals(p1, p2)  
  || geometry::equals(p2, p3) )  
{  
}  
```  
  
than this:  
  
```  
if (geometry::equals(p1, p2)  
 || geometry::equals(p2, p3))  
{  
}  
```  
  
Not to mention more complex conditions.  
What do you think?

---

## Comment 26

> Username: awulkiew  
> Created_at: 2014-10-15 14:24:28 UTC  
> Updated_at: 2014-10-15 14:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18896370  

Yes, this was copied from distance_cross_track, I'll fix it. Maybe then the original should also be fixed?

---

## Comment 27

> Username: awulkiew  
> Created_at: 2014-10-15 14:33:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18897056  

done

---

## Comment 28

> Username: mkaravel  
> Created_at: 2014-10-16 15:36:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18963690  

Thank you

---

## Comment 29

> Username: mkaravel  
> Created_at: 2014-10-16 15:37:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18963761  

Yes, I believe so. The 80 characters limit is a boost requirement.

---

## Comment 30

> Username: mkaravel  
> Created_at: 2014-10-16 15:37:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18963819  

You are right. It was a bug. It is okay as it is right now. Thanks for fixing this.

---

## Comment 31

> Username: mkaravel  
> Created_at: 2014-10-16 15:39:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#discussion_r18963976  

I agree with Adam: I do not particularly like this guideline either, which is why I suggested to add the spaces in the first place.  
  
@barendgehrels What do you think? Can we relax this to allow for one space character?

---

## Comment 32

> Username: mkaravel  
> Created_at: 2014-10-16 15:40:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#issuecomment-59382161  

I am fine with merging this PR.

---

## Comment 33

> Username: barendgehrels  
> Created_at: 2014-10-18 10:50:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/132#issuecomment-59607048  

I did not have the time to do a close look at the code itself, but I'm fine with merging

---
