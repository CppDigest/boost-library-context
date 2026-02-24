# #460 Feature/simplify [Merged]

> Username: barendgehrels  
> Created at: 2018-02-14 16:34:04 UTC  
> Updated at: 2018-02-26 22:11:58 UTC  
> Merged at: 2018-02-19 17:50:11 UTC  
> Closed at: 2018-02-19 17:50:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/460  

The simplify feature has been improved such that:  
  
- closing point of a polygon can be part of simplification process  
- simplified output rings with zero area, or reversed area, are omitted from output  
- simplification with resulting duplicate point is corrected, duplicated point is omitted  
- consistent behaviour for small polygons (e.g. triangles) and pentagons (they could be reduced to two points earlier)  
- updated tests   
- added unit test using countries (cf buffer), including SVG. This is optional (not compiled by default). It gives good insight in the simplification process, and showed the earlier version was not yet good enough (most islands became spikey polygons)

---

## Review 1 [Commented]

> Username: vissarion  
> Created_at: 2018-02-15 10:31:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/460#pullrequestreview-96798797  

📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 216 |             simplify_closure_inspector::get_start_end(start, end, ring,
 213 |-                     max_distance, strategy);
 217 |+                     max_distance / 10.0, strategy);
```

> Username: vissarion  
> Created_at: 2018-02-15 10:31:55 UTC  
> Updated_at: 2018-02-19 17:42:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#discussion_r168435340  

Where does value 10% come from? Is it a well known "open problem" or it comes from some testcase?


---

## Review 2 [Commented]

> Username: vissarion  
> Created_at: 2018-02-15 10:38:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/460#pullrequestreview-96800682  

📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 245 |+             // TODO: for open polygons, implementation should be modified
 246 |+             simplify_closure_inspector::get_start_end(start, end, ring,
 247 |+                     max_distance / 2.0, strategy);
```

> Username: vissarion  
> Created_at: 2018-02-15 10:38:19 UTC  
> Updated_at: 2018-02-19 17:42:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#discussion_r168436972  

Where does value 2.0 come from? Is it empirical (it was 10.0, 1000.0 etc in previous versions)? Is it a well known "open problem" or it comes from some testcase?

> Username: barendgehrels  
> Created_at: 2018-02-15 17:28:43 UTC  
> Updated_at: 2018-02-19 17:42:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#discussion_r168549370  

The "open problem" is described here: http://cs.smith.edu/~jorourke/TOPP/P24.html#Problem.24  
I did not look in more detail to those cited sources. But I understand the problem, you have to have a starting point (previously the closing point) and it is more or less an arbitrary starting point. If you rotate the polygon and do the simplification again, you might get something different.  
This solution mitigates this problem by potentially simplifying the area around the closing point - but that is a different simplification, it still does not guarantee the optimal solution.  
  
The 1000.0 earlier was actually hiding another bug in the first version (forgot to close the result), and I wanted to drastically reduce that later (10), but that is also arbitrary. And it did show some artefacts (but that can also be random). The value of 2.0 is still arbitrary too, it must be "some" value smaller than specified distance. But this did not give artefacts.  
  
I will send you a picture later or maybe we can inlude them even in this review (Greece is one of the testcases!)

> Username: vissarion  
> Created_at: 2018-02-16 11:48:56 UTC  
> Updated_at: 2018-02-19 17:42:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#discussion_r168737104  

But this is a problem of the strategy (i.e. Douglas-Peucker algorithm) that offer no guarantees for the maximum distance between the original and the simplified curve. Therefore, should this heuristic of decreasing `max_distance` be inside that strategy?


---

## Comment 3

> Username: vissarion  
> Created_at: 2018-02-15 10:47:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#issuecomment-365890790  

Thanks! I reviewed the code and I am ok for merging.   
  
A more general question: is there an example in tests where simplify destroys validity?

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2018-02-15 17:32:52 UTC  
> Updated_at: 2018-02-15 17:33:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#issuecomment-366002562  

@vissarion thanks for your review!  
Yes, simplify can indeed destroy validity, and it does (I have some testcases). This PR enhances this (polygons with zero area omitted, empty polygons omitted) but apart from that, rings previously valid can start to self-cross. Users an call "dissolve" afterwards (if released), and PostGIS has a ST_SimplifyPreserveTopology algorithm, which we could also think about in the future.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2018-02-15 18:06:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#issuecomment-366012238  

![simplify_enhancement_feb_2018](https://user-images.githubusercontent.com/334849/36272959-4b3b4a96-1283-11e8-8f26-9dfd26a93674.jpg)  
  
Image showing enhanced (left) and original (right)

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2018-02-15 18:55:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#issuecomment-366026060  

BTW Boost.Geometry can do a better job if you use the known method of inflating a multi-polygon, followed by deflating it (+optionally finish with simplifying that result - and selecting on area). This way it stays valid (and it takes much longer).  
Also an advantage is that islands close together can be merged - or merged to mainland  
  
![simplify_with_buffer_feb_2018](https://user-images.githubusercontent.com/334849/36274982-c953f418-1289-11e8-8b34-365047bdf100.jpg)

---

## Comment 7

> Username: vissarion  
> Created_at: 2018-02-16 11:13:55 UTC  
> Updated_at: 2018-02-16 11:16:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#issuecomment-366208507  

Thanks for the nice pictures of Greece!   
  
Regarding validity indeed postgis (actually geos) has a function that preserves topology by modifying the Douglas-Peucker algorithm. I guess that Boost.Geometry's implementation is the one of the standard algorithm, or have some heuristics?   
  
Indeed it would be interesting to have a topology preserving variant in the future. What you do with buffer preserves validity but it seems that uses a lot of points (at least from the picture).   
  
Also that link here is broken: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/agnostic/simplify_douglas_peucker.hpp#L260

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2018-02-17 10:53:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#issuecomment-366433278  

Thanks for your comments @vissarion   
  
Yes, the buffer solution is completely different indeed. It uses the same specified distance, but not for the distance between the points, but it is then the distance to inflate/deflate.  
  
I currently have not yet a concrete idea how to keep output valid using Douglas-Peucker (we indeed use the std implementation), especially because in a multipolygon, or with interiors, the simplified lines might easily start to cross each other (I have many samples of this).  
  
Then about the smaller distance for the closing area. Your comment did let me rethink about this and I now realize that the new implementation might cause new artefacts (as is in fact visible at the island Kos). That can be solved by rotating the iterator, instead of current slicing, because the artefact might appear at the slice begin-end (which might be mitigated but not completely solved). I did initially think about the rotational iterator/range, but because we don't have it, and it is not in Boost (as far as I know), I took the slicing. Alternatively we can rotate the whole range, and maybe we should do that initially (to verify that behaviour), but it is less performant because it copies the range.  
  
So this (the artefacts) can be seen as a regression, we can postpone this merge, I have to fix it. Selecting another rotation (in the sense of iterators) point will not cause such artefacts (because it is just as if the polygon starts/closes there - each polygon can be rotated over all its point while staying spatially equal).   
  
Rotation still does not guarantee the "optimal" solution - but that can be accepted.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2018-02-17 13:07:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#issuecomment-366440378  

Based on Vissarions comment and my reply, new implementation using rotate. This is again improving the result and removing the artifacts.  
Actually it is not so bad that it is not (yet) a rotational view - also we should have the closing point there, vector is fast, and implementation is now faster anyway because I check before if the result will be empty.  
Rotation is also safer to use, as I also stated above.  
  
![simplify_using_rotate](https://user-images.githubusercontent.com/334849/36341227-6b96a0d8-13eb-11e8-9b56-671fc7363c18.png)  
  
The new result where the artefact in Kos is gone, also Limnos is know valid (but this does still not mean that validity is guaranteed)

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2018-02-18 17:03:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#issuecomment-366530239  

One more update, I realized that certain cases (having another collinear series of points right at end of closing area) would not be solved. This changes the solution: I now start with the farthest point (from closing point). If I'm right that is never such a collinear point (though in a circle, it nearly is), so that fixes that, and the "closing area" in general (which is now removed).  
Also, if I'm right, any farthest is always on the convex hull (at least most probably), so it also fixes cases such as that spike on the Lesbos island (which was the starting point) in earlier pictures.  
Also it reduces artifacts (because the starting point might not be optimal and result in an elongated triangle - but if it is the farthest point, that is much less probable).  
  
It now looks like:  
![simplify_rotate2](https://user-images.githubusercontent.com/334849/36354448-ee5ee1ee-14d4-11e8-9c0c-59f3b6d526b9.png)  
  
Additionally, to avoid empty output (which can happen, it is just a kind of random process), the algorithm now tries once more at 1/4 of the polygon (and if then not found, 2 times more). That is actually optional but takes (most probably) care that polygons which could have any output, with any starting point, will have output (though it does not guarantee it - to guarantee optimal solution, all starting points on convex hull should be tried...)

---

## Review 11 [Commented]

> Username: awulkiew  
> Created_at: 2018-02-18 23:39:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/460#pullrequestreview-97420856  

I'm ok with merging. The remarks I made are cosmetic.

📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 107 |- 
 108 |-         if (boost::size(range) <= int(Minimum) || max_distance < 0.0)
 109 |+         if (boost::size(range) <= static_cast<int>(MinimumToUseStrategy) || max_distance < 0)
```

> Username: awulkiew  
> Created_at: 2018-02-18 23:15:53 UTC  
> Updated_at: 2018-02-19 17:42:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#discussion_r168970116  

`boost::size` returns unsigned type so my guess is that casting is not needed and can cause a comparison warning.

---

📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 123 |+         if (boost::size(out) == 2
 124 |+             && geometry::equals(geometry::range::front(out),
 125 |+                     geometry::range::back(out)))
```

> Username: awulkiew  
> Created_at: 2018-02-18 23:22:58 UTC  
> Updated_at: 2018-02-19 17:42:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#discussion_r168970329  

This is the second time this condition is present in the code. This check could be implemented as a free function.

---

📁 include/boost/geometry/algorithms/simplify.hpp

```diff
  68 |+             std::copy(boost::begin(range), boost::begin(range) + 1, out);
  69 |+         }
  70 |+         else if (boost::size(range) <= 2 || max_distance < 0)
```

> Username: awulkiew  
> Created_at: 2018-02-18 23:24:03 UTC  
> Updated_at: 2018-02-19 17:42:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#discussion_r168970354  

Do these conditions work the same way for closed and open rings? Shouldn't the size be `3` in case of closed ring? Or is some particular closure assumed?

---

📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 229 |+             // and what was negative stays negative (or goes to 0)
 230 |+             int const output_sign = area_sign(geometry::area(out));
 231 |+             if (output_sign == input_sign)
```

> Username: awulkiew  
> Created_at: 2018-02-18 23:27:46 UTC  
> Updated_at: 2018-02-19 17:42:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#discussion_r168970455  

Is this the check for the same orientation? If that's the case maybe we should have a function for that? To not use area explicitly because the actual area is not used here.

> Username: barendgehrels  
> Created_at: 2018-02-19 17:44:14 UTC  
> Updated_at: 2018-02-19 17:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#discussion_r169140101  

Indeed, it checks for orientation only, that what was positive should stay positive, and what was negative should stay negative.  
So yes, good idea to have a function for that (though, if it is modelles as "orientation_equal(a,b)", it would have less performance than here because it has to recalculate area of input more than once)

> Username: awulkiew  
> Created_at: 2018-02-19 20:31:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#discussion_r169166641  

I rather was thinking about making the code more self-descriptive. The area is not important here and may be confusing. The orientation is checked so if the function name reflected that then the coment wouldn't be needed because the code itself would make it clear. Furthermore enum type would be more appropriate than `int` to store the result. Therefore it could be something like `get_ring_orientation(a)` returning value of type `bg::order_selector` so `bg::clockwise`, `bg::counterclockwise ` or `bg::order_undetermined`.

> Username: barendgehrels  
> Created_at: 2018-02-19 20:56:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#discussion_r169170383  

That is a good idea indeed.

---

📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 157 |-         traits::resize<InteriorRingsOut>::apply(interior_rings_out,
 158 |-             boost::size(interior_rings_in));
 298 |+         traits::clear<InteriorRingsOut>::apply(interior_rings_out);
```

> Username: awulkiew  
> Created_at: 2018-02-18 23:28:46 UTC  
> Updated_at: 2018-02-19 17:42:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#discussion_r168970491  

FYI, there is `geometry::range::clear(interior_rings_out)`.

---

📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 339 |+             if (! geometry::is_empty(single_out))
 340 |+             {
 341 |+                 traits::push_back<MultiGeometry>::apply(out, single_out);
```

> Username: awulkiew  
> Created_at: 2018-02-18 23:30:17 UTC  
> Updated_at: 2018-02-19 17:42:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#discussion_r168970546  

`geometry::range::push_back(out, single_out);`

---

📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 329 |     {
 197 |-         traits::resize<MultiGeometry>::apply(out, boost::size(multi));
 330 |+         traits::clear<MultiGeometry>::apply(out);
```

> Username: awulkiew  
> Created_at: 2018-02-18 23:30:34 UTC  
> Updated_at: 2018-02-19 17:42:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#discussion_r168970571  

`range::clear(out);`

---

📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 219 |+ 
 220 |+             std::rotate_copy(boost::begin(ring), boost::begin(ring) + index,
 221 |+                              boost::end(ring), rotated.begin());
```

> Username: awulkiew  
> Created_at: 2018-02-18 23:36:59 UTC  
> Updated_at: 2018-02-19 17:42:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#discussion_r168970767  

`boost::begin(ring) + index` could be replaced with `range::pos(ring, index)`. My guess is that because index is of unsigned type and the random access iterator is advanced with difference_type which is signed there can be a conversion warning generated here.


---

## Comment 12

> Username: barendgehrels  
> Created_at: 2018-02-19 17:43:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#issuecomment-366762200  

@awulkiew  thanks for your review. I applied your comments and will answer your questions and merge.

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2018-02-19 17:49:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#issuecomment-366763651  

Answer on this question: _Do these conditions work the same way for closed and open rings? Shouldn't the size be 3 in case of closed ring? Or is some particular closure assumed?_  
At that point, yes, it works the same way there.  
In general, open polygons should still be visited because I now realize well that these could create artefacts in the past (because it simplified from start to end, which might be located very close together). Now that is solved automatically. The thing to be done is that they should be reopened (correct closure). I will do that later (though it is one line), because I want to add a unit test for that case.

---

## Comment 14

> Username: awulkiew  
> Created_at: 2018-02-19 20:42:34 UTC  
> Updated_at: 2018-02-19 20:42:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#issuecomment-366798194  

Btw, `douglas_peucker` is an agnostic strategy which takes Pt/Seg distance strategy. So theoretically it should work with other coordinate systems if non-cartesian Pt/Seg distance strategy was passed. In that case when calculating the orientation the area strategy should be derived from simplify strategy which should derive it from distance strategy. But this could be done later.

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2018-02-19 20:58:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/460#issuecomment-366801286  

Right. Yes. BTW, I verified that it works, adding another test (simplify on a more complex latlong geometry in spherical coordinates), but I did not commit that yet (there is already a spherical test - but we should add geographic too, and indeed with a large coverage for the area check).  
Thanks again for your comments.

---
