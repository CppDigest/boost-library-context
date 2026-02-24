# #289 New feature: envelope and expand for spherical equatorial coordinate system [Merged]

> Username: mkaravel  
> Created at: 2015-05-04 10:32:27 UTC  
> Updated at: 2015-05-28 18:04:07 UTC  
> Merged at: 2015-05-28 18:04:07 UTC  
> Closed at: 2015-05-28 18:04:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/289  

This PR implements (or re-implements) the algorithms `bg::envelope` and `bg::expand`.  
  
As far as the old implementation is concerned, the new approach does use `bg::assign_inverse` as it might not be well-defined for user-defined number types.  
  
Regarding the new implementation, the following are implemented for the spherical equatorial and possibly geographic coordinate systems (see lists below for which combinations support geographic coordinate systems; "spherical" below refers to "spherical equatorial"):  
#### expand algorithm  
- `expand(box, point)` (spherical and geographic)  
- `expand(box, segment)` (spherical only)  
- `expand(box, box)` (spherical and geographic)  
#### envelope algorithm  
- `envelope(point, mbr)` (spherical and geographic)  
- `envelope(segment, mbr)` (spherical only)  
- `envelope(box, mbr)` (spherical and geographic)  
- `envelope(linestring, mbr)` (spherical only)  
- `envelope(multilinestring, mbr)` (spherical only)  
- `envelope(multipoint, mbr)` (spherical and geographic)  
  
This PR depends on PR #271, as it requires the coordinate normalization functionality introduced in that PR.  
  
**Note 1:** due to the changes introduced in this PR, and in particular how MBRs are computed on the sphere/spheroid, one of the distance unit tests fails. This is a known issue and will be addressed after this PR is reviewed and merged.  
  
**Note 2:** what is still missing is the implementation of the `envelope` algorithms for areal geometries (rings, polygons and multipolygons). This is still under development and will either be added to this PR, or through a new PR at a later point.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-05-04 14:15:07 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29587272  

Is `apply()` taking 4 coordinates used somewhere outside this struct? Is there a reason why the coordinates aren't normalized in this overload? This way it works slightly differently than the other 2 overloads which can be confusing. It should be consistent with the two, moved somewhere else as a separate (low-level) tool or probably most preferably made private.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-05-04 14:16:29 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29587373  

Here `detail::indexed_point_view<>` could be used and point view objects passed into `apply()` taking 2 points. To not duplicate the implementation.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-05-04 14:18:17 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29587561  

Could `detail::indexed_point_view` be used here instead?

---

## Comment 4

> Username: awulkiew  
> Created_at: 2015-05-04 14:34:15 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29588927  

Here we have the same pattern as above (in `intersects_antimeridian`):  
1. create variables contain coordinates  
2. normalize the coorinates  
3. convert to radians (this isn't done above)  
  
I'm guessing that this pattern can be used often. Would it be a good idea to have one well-defined function for this instead of several small ones?  
  
Why does `convert_coordinates` was added? We already have `get_as_radian`. I'm guessing that `normalize_spheroidal_coordinates` are normalized using the native units (radians or degrees). Is it safe? Let's say the coordinates are normalized using degrees and then converted to radians, are the radians normalized? I'm guessing that in some cases this may not hold. I think that first the coordinates should be converted to radians and then normalized. Furthermore I think it should be done this way in all algorithms for consistency. Therefore we probably doesn't need those generic utils you added for conversion, even more, they can be harmful. We should be forced to convert the units always the same way in all functions. Most preferably we could have single one function doing it.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2015-05-04 14:42:03 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29589590  

And here `set_from_radian` could be used, so a tool which we already have and was exactly designed for this purpose. The code above could use `detail::indexed_point_view` and `set_from_radian`.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2015-05-04 14:56:35 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29590902  

I'm wondering, why was this struct added? Do you think that using `std::pair` is not clear enough or is there some other reason?

---

## Comment 7

> Username: awulkiew  
> Created_at: 2015-05-04 14:57:17 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29590958  

The same pattern again, get coordinates and normalize.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2015-05-04 14:59:37 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29591161  

Can the result of those comparisons be different for degrees and radians? I'm wondering, maybe we should always use radians internally to be consistent with other functions always converting degrees to radians?

---

## Comment 9

> Username: awulkiew  
> Created_at: 2015-05-04 16:20:30 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29598245  

The same pattern again. Maybe we should have `bg::detail::normalize()` or `bg::detail::return_normalized()` algorithm working for Points, Boxes and Segments?  
This code could be:  
  
```  
detail::indexed_point_view<Box, min_coordinate> p_min(mbr);  
detail::indexed_point_view<Box, max_coordinate> p_max(mbr);  
bg::convert(bg::return_normalized<box_point_type>(point), p_min);  
bg::convert(bg::return_normalized<box_point_type>(point), p_max);  
```  
  
or something similar.  
  
Or maybe `convert()` could be modified this way, to convert point into box in the right way for all coordinate systems?

---

## Comment 10

> Username: awulkiew  
> Created_at: 2015-05-04 16:23:40 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29598511  

Should we use `mbr` name? It isn't widely used in the library, isn't it? Usually we use the name `box`. Besides a type is called Box, all types and variables below contains a word `box`.  
  
EDIT: this is a general remark, since in many functions a word `mbr` is used for parameter name.

---

## Comment 11

> Username: awulkiew  
> Created_at: 2015-05-04 16:33:33 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29599260  

Are the expand policies really needed? AFAIU a single call to `expand()` for a Polygon should do exactly the same thing. The same for `default_expand_policy` below. It's only purpose is to run `expand()`. Or is there some specific case where a single call of `expand()` wouldn't work?

---

## Comment 12

> Username: awulkiew  
> Created_at: 2015-05-04 16:50:15 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29600631  

Please ignore the above comment. I see now that what is needed is to expand a box with an envelope of a Geometry. But since this code implements the `envelope()` this algorithm can't be used like a user would do this:  
  
```  
expand(box, return_envelope<Box>(geometry));  
```  
  
Btw, shouldn't it be called `polygon_expand_policy` since it must take a Polygon?

---

## Comment 13

> Username: awulkiew  
> Created_at: 2015-05-04 17:03:42 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29601696  

TODOs here and above.  
  
The input Boxes can be different than the output (units, coordinate_type) so I'm guessing that the conversion should be made at some point. Currently the coordinate_type and units of the input Boxes is used but I'm not sure if it wouldn't be better to convert the input coordinates to the output coordinate_type and use it for the intervals. Or to be consistent with other algorithms always use radians and the most precise type?  
  
But it depends, will this algorithm be publicly available? Or is it only for internal use e.g. in partition and rtree?

---

## Comment 14

> Username: mkaravel  
> Created_at: 2015-05-04 17:55:29 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29606404  

Actually, the code in this file is not used within the rest of code currently submitted in this PR.  
It is used right now in my prototype (not pushed) implementation for rings, and was accidentally pushed as part of this PR.  
Please disregard it for now, and we can come back later to it.

---

## Comment 15

> Username: mkaravel  
> Created_at: 2015-05-04 17:55:53 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29606444  

Will do. Thanks for the tip.

---

## Comment 16

> Username: mkaravel  
> Created_at: 2015-05-04 17:57:34 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29606591  

I would say yes. I do not really see why it should not work.  
I will try it and get back to you.

---

## Comment 17

> Username: mkaravel  
> Created_at: 2015-05-04 17:59:06 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29606753  

I will get back to these comments later.

---

## Comment 18

> Username: mkaravel  
> Created_at: 2015-05-04 18:01:35 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29607028  

I think the reason I did not use `set_from_radian` is that:  
1. I think it does not work with boxes (it takes only one index template parameter, and most probably we should change that...)  
2. I think it does not do any conversion _or_ the conversion done is implicit, which I wanted to avoid.  
  
In any case I will check again if it can/should be done the way you suggest,

---

## Comment 19

> Username: mkaravel  
> Created_at: 2015-05-04 18:06:41 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29607502  

I think the code is more readable with a properly named struct for the pair, but this is just my personal point of view.

---

## Comment 20

> Username: mkaravel  
> Created_at: 2015-05-04 18:08:50 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29607726  

I do not agree with using radians all over the place.  
My personal preference would be to stick to the original coordinate type if possible, and only convert to radians if needed.  
For example here, the way I have implemented it, it works perfectly well with points whose coordinate type is an integer and the units are degrees: all computations are done with integers.

---

## Comment 21

> Username: mkaravel  
> Created_at: 2015-05-04 18:10:22 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29607864  

The old code was using `mbr` in several places, and this is what inspired me.  
I like the name `mbr` as it clearly indicates that the output is a minimum bounding rectangle.  
I can change it if you want, though.

---

## Comment 22

> Username: mkaravel  
> Created_at: 2015-05-04 18:11:52 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29607992  

I wanted to avoid the creation of new points, which is why I consistently work with the coordinates.  
I do agree though that `bg::convert` may have to be changed and basically use that instead.

---

## Comment 23

> Username: mkaravel  
> Created_at: 2015-05-04 18:13:30 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29608141  

This is meant to be part of the internals and not exposed.  
We can discuss if we really need to expose it.  
Given its current usage, no conversion is necessary at this point; it happens somewhere else.

---

## Comment 24

> Username: mkaravel  
> Created_at: 2015-05-04 18:14:33 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29608234  

As I mentioned in another comment, I would like to avoid conversions to radians if not needed. This is certainly one case where we do not need the conversion to radians for the algorithm to work, so I prefer to keep/use the input coordinate type.

---

## Comment 25

> Username: mkaravel  
> Created_at: 2015-05-04 21:02:08 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29623358  

I will try to answer your comments/questions one-by-one.  
  
_I'm guessing that this pattern can be used often. Would it be a good idea to have one well-defined function for this instead of several small ones?_  
  
I will think about it, and yes it would make sense. I would even argue that the correct place to put all these is in `bg::convert()`.  
  
_Why does `convert_coordinates` was added? We already have `get_as_radian`._  
  
I want to first normalize and then convert. I find this more robust.  
  
_I'm guessing that `normalize_spheroidal_coordinates` are normalized using the native units (radians or degrees)._  
  
Correct.  
  
_Is it safe?_  
  
Why do you think it would not be safe? What kind of safety do you refer to?  
  
_Let's say the coordinates are normalized using degrees and then converted to radians, are the radians normalized? I'm guessing that in some cases this may not hold._  
  
Yes, the radians are normalized. Normalization has nothing to do with the specific units you use, and you can express it in either degrees or radians (or any other units, like seconds of degrees). Normalization basically tells you that latitudes go from `-period/4` to `+period/4` and longitudes go from `-period/2` to `period/2`. The actual value for `period` depends on the units you choose.  
  
_I think that first the coordinates should be converted to radians and then normalized._  
  
Why do you think so? When you convert to radians first you immediately enter the floating-point world, even for integral coordinates. I would argue that you should delay that as long as possible.  
  
_Furthermore I think it should be done this way in all algorithms for consistency. Therefore we probably doesn't need those generic utils you added for conversion, even more, they can be harmful. We should be forced to convert the units always the same way in all functions. Most preferably we could have single one function doing it._  
  
So far we have been converting to radians because we needed to feed coordinates to trigonometric functions; this is absolutely understandable. But if you do not need to use trigonometric functions why bother converting?  
I do not follow you regarding the _harmful_ part. I agree that we could/should encapsulate conversion into a single function, but I do not (not yet at least) get what is problematic with my approach.

---

## Comment 26

> Username: mkaravel  
> Created_at: 2015-05-04 21:06:32 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29623726  

It seemed to me more natural to name it according to how the geometry is used in the policy (taking its exterior ring).

---

## Comment 27

> Username: mkaravel  
> Created_at: 2015-05-04 21:09:51 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29624012  

The same comments apply to both `TODOs` in the code: if we are to expose the algorithm implemented in `envelope_range_of_boxes` further, then both normalization and conversion might be needed explicitly here. as it is currently part of the internal implementation, the `TODOs` are not really needed.

---

## Comment 28

> Username: mkaravel  
> Created_at: 2015-05-04 21:15:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#issuecomment-98851513  

@awulkiew Thanks for your comments. Looking forward to your replies and further suggestions.

---

## Comment 29

> Username: awulkiew  
> Created_at: 2015-05-04 21:40:39 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29626682  

Ok, I get it. For me personally it isn't important how the expansion is implemented. It uses only the exterior ring because this is how we generally implement things for Polygon but it might do something else. It'd still expand a boxy using an envelope of a Polygon.  
  
There is also `envelope_polygon` which also internally analyses only the exterior ring of a Polygon and `envelope_range_expand_policy` which expands a box using an envelope of a range. Well, the last name can also be confusing because this policy works on a Range of Points, not Range of envelopes (so boxes?).

---

## Comment 30

> Username: awulkiew  
> Created_at: 2015-05-04 22:07:39 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29628827  

Indeed it should work better in the case of integral degrees, but AFAIU only in this case. For FP degrees normalized and converted to FP radians it shouldn't change anything.  
  
My point is, it probably isn't possible to represent true 90 as it certainly isn't possible to represent true pi/2. And while converting between both units we can loose information. It's just a guess but in some cases, for some input in FP degrees or some user-defined type the value after conversion could be outside the range e.g. [-pi/2, pi/2] for latitude. I just think it'd be safer to normalize exactly those values that can be passed into some trigonometric functions. The radians are exactly those values. Maybe the radians should also be normalized (2nd time) or maybe only the integral degrees should be normalized at the beginning (and FP radians always normalized at the end)?

---

## Comment 31

> Username: awulkiew  
> Created_at: 2015-05-04 22:12:10 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29629160  

This is why I proposed to use `indexed_point_view`. I just don't like the idea of implementation of several tools for the same thing. There should be one method widely used across the whole library.

---

## Comment 32

> Username: awulkiew  
> Created_at: 2015-05-04 22:16:04 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29629405  

Yes, indeed using the original coordinates wherever possible seems to be a better idea.

---

## Comment 33

> Username: awulkiew  
> Created_at: 2015-05-04 22:18:50 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29629617  

Ok, I don't have a strong opinion in this case so let's see what others will say.

---

## Comment 34

> Username: awulkiew  
> Created_at: 2015-05-04 22:25:28 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r29630060  

Indeed, the example above uses 2 temporary points. But only one Point could be used and reused, so also 2 coordinates (similarly to your code). Or do you fear that a user-defined Point could contain some additional members? Anyway, how about this:  
  
```  
detail::indexed_point_view<Box, min_coordinate> p_min(mbr);  
detail::indexed_point_view<Box, max_coordinate> p_max(mbr);  
bg::convert(bg::return_normalized<box_point_type>(point), p_min);  
bg::convert(p_min, p_max);  
```  
  
which directly initializes Box's min corner and then copies it to max. So no temporaries at all.  
  
EDIT: A temporary is returned from `return_normalized`, it should be optimized out. But also in your case the temporaries should be optimized out. So the effect should be the same but in the above example the code is shorter and more readable.

---

## Comment 35

> Username: barendgehrels  
> Created_at: 2015-05-13 11:27:52 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30221827  

Agreed, a std::pair is (on longer term) often confusing

---

## Comment 36

> Username: barendgehrels  
> Created_at: 2015-05-13 11:30:02 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30221957  

I don't object to mbr  
(But the OGC term is "envelope" EDIT: but of course we are in that namespace so should not use that here)

---

## Comment 37

> Username: barendgehrels  
> Created_at: 2015-05-13 11:31:55 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30222062  

We normally don't do onlined multiple assignments in the library, I prefer to split

---

## Comment 38

> Username: barendgehrels  
> Created_at: 2015-05-13 11:34:21 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30222181  

Please assign as bool har_north_pole = false;

---

## Comment 39

> Username: barendgehrels  
> Created_at: 2015-05-13 11:34:51 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30222201  

brackets are redundant

---

## Comment 40

> Username: barendgehrels  
> Created_at: 2015-05-13 11:35:10 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30222216  

As above please avoid = .. =

---

## Comment 41

> Username: barendgehrels  
> Created_at: 2015-05-13 11:36:47 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30222283  

Now that you introduced a lon_lat_pair, we should not use first/second but lon/lat  
Why it is actually not a geometry point_type that is used here?

---

## Comment 42

> Username: barendgehrels  
> Created_at: 2015-05-13 11:37:11 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30222305  

(minor) coordinates.empty()

---

## Comment 43

> Username: barendgehrels  
> Created_at: 2015-05-13 11:43:07 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30222645  

Is it possible to do calculate the max gap without sorting?

---

## Comment 44

> Username: barendgehrels  
> Created_at: 2015-05-13 11:43:29 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30222665  

= 0, = 0

---

## Comment 45

> Username: mkaravel  
> Created_at: 2015-05-13 14:36:52 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30236454  

I am responding to this, but it also applies to other similar comments: AFAIR,  
  
```  
some_type x = 0;  
```  
  
and  
  
```  
some_type x(0);  
```  
  
are treated in exactly the same way by the C++ standard: in both cases the constructor is called with the value `0` as argument.  
Is your comment related to BG's coding style or is it something else?

---

## Comment 46

> Username: mkaravel  
> Created_at: 2015-05-13 14:37:28 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30236514  

Is that a matter of coding style, or do you have in mind something else?

---

## Comment 47

> Username: mkaravel  
> Created_at: 2015-05-13 14:46:51 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30237663  

Is your question really about sorting, or whether we can do it in linear time as opposed to linearithmic time?  
  
I will try to answer both:  
1. Yes we can do away without sorting. For example, we can use the sweeping framework I have developed as part of this PR. Instead of sorting all longitudes, I can push them in a priority queue and successively pop them, in order to compute the maximum gap. But I am not really sure we gain something using the sweep approach as opposed to sorting.  
2. Yes, it is possible to compute the maximum gap in linear time. There is a bin-based algorithm, that also requires the use of the `floor()` function. You may take a look at [these slides](http://cgm.cs.mcgill.ca/~godfried/teaching/dm-reading-assignments/Maximum-Gap-Problem.pdf), explaining this bin-based linear time algorithm. I am not convinced that in practice it would be faster. I can certainly implement it, either now or at a later time and do benchmarks.

---

## Comment 48

> Username: barendgehrels  
> Created_at: 2015-05-13 15:08:53 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30240316  

It is coding style

---

## Comment 49

> Username: barendgehrels  
> Created_at: 2015-05-13 15:10:41 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30240560  

Thanks for explaining - I'm OK by doing it like you did and benchmark later

---

## Comment 50

> Username: barendgehrels  
> Created_at: 2015-05-13 15:13:45 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30240956  

There have been quite some correspondence about this.  
The BG style was: CoordinateType max_gap_left = CoordinateType()  
That can be necessary in template environments (to support big numeric types) but not always.  
But using elementary types as if it is a constructor (bool, int) suddenly popped up once.  
What does C++11, C++14, C++17 prefer?

---

## Comment 51

> Username: barendgehrels  
> Created_at: 2015-05-13 15:15:11 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30241127  

E.g. here you also don't write:  
`CoordinateType max_gap ( maximum_gap ( ... ) )`  
Which would be indeed confusing. So I prefer (personally) to don't do it for 0 neither

---

## Comment 52

> Username: barendgehrels  
> Created_at: 2015-05-13 15:18:16 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30241436  

I agree with making and using such a xxx normalized for this

---

## Comment 53

> Username: barendgehrels  
> Created_at: 2015-05-13 15:20:50 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30241739  

Looks good

---

## Comment 54

> Username: barendgehrels  
> Created_at: 2015-05-13 15:21:15 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30241795  

curly brace goes next

---

## Comment 55

> Username: barendgehrels  
> Created_at: 2015-05-13 15:24:48 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30242206  

This is in a file detail, but in namespace boost::geometry  
Is that intentional? Can it in the end be called by an end-user?  
  
Maybe the sweep requires more documentation?

---

## Comment 56

> Username: mkaravel  
> Created_at: 2015-05-13 15:43:02 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30244414  

I do not like the initialization with the default constructor you mention in another comment.  
  
I am okay with either solution (using `=` or pass value to constructor). I guess from the readability point of view using `=` is better, and I would argue that this should be part of our coding rules.

---

## Comment 57

> Username: mkaravel  
> Created_at: 2015-05-13 15:44:08 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30244555  

Yes, it is intentional. `bg::sweep()` is something like `bg::partition()` in my mind. At some point I would like to document it, but not yet.

---

## Comment 58

> Username: barendgehrels  
> Created_at: 2015-05-13 15:47:29 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30244924  

OK, good

---

## Comment 59

> Username: barendgehrels  
> Created_at: 2015-05-13 15:48:25 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30245032  

I would like to see it in our coding rules too. I'm OK with the good old standard =

---

## Comment 60

> Username: mkaravel  
> Created_at: 2015-05-13 16:35:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#issuecomment-101739507  

@barendgehrels Thank you for your comments.  
  
I will update the code and ask for a second round of review once I am done.

---

## Comment 61

> Username: awulkiew  
> Created_at: 2015-05-15 19:39:05 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30439334  

Value initialization as you wrote it (with small differences between standards) means in this case: initialize with 0 for built-in types and call default ctor for user-defined types. This is a wrong approach since the user-defined type could implement the default ctor not initializing the value to 0. In particular this is true for `ttmath_big`. It we want to initialize with `0` we should explicitly initialize with 0, in the whole library. Just like we're doing for other numbers 2, 4, 5243, etc. Yes, we're already initializing explicitly. Why do you think 0 is different?

---

## Comment 62

> Username: awulkiew  
> Created_at: 2015-05-15 19:51:48 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30440406  

I'm ok with both since it's valid C++. I agree that using assignment is probably more clear since the other version looks like a function declaration. And in some cases it can be treated as such by standard-conformant compiler, then it requires additional parentheses, etc. On the other hand by using assignment operator we dissalow to use user-defined classes with explicit copy constructor. Though such classes won't be usable in Geometry anyway right now since various methods of initializations are mixed across the library.

---

## Comment 63

> Username: awulkiew  
> Created_at: 2015-05-15 19:53:26 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30440519  

Indeed, I agree that initialization of multiple variables in separate lines is more clear.

---

## Comment 64

> Username: mkaravel  
> Created_at: 2015-05-20 08:54:50 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30681936  

Done.

---

## Comment 65

> Username: mkaravel  
> Created_at: 2015-05-20 08:56:02 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30682023  

I prefer to stick with `mbr`. I find it very descriptive.

---

## Comment 66

> Username: mkaravel  
> Created_at: 2015-05-20 08:56:38 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30682059  

The `TODOs` are gone and replaced by relevant comments.

---

## Comment 67

> Username: mkaravel  
> Created_at: 2015-05-20 08:56:54 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30682081  

Fixed.

---

## Comment 68

> Username: mkaravel  
> Created_at: 2015-05-20 08:57:28 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30682121  

Fixed.

---

## Comment 69

> Username: mkaravel  
> Created_at: 2015-05-20 08:58:00 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30682156  

The `lon_lat_pair` is now gone. I am using points instead.  
The code should be more readable now.

---

## Comment 70

> Username: mkaravel  
> Created_at: 2015-05-20 08:58:10 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30682173  

Done.

---

## Comment 71

> Username: mkaravel  
> Created_at: 2015-05-20 08:58:33 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30682203  

Fixed.

---

## Comment 72

> Username: mkaravel  
> Created_at: 2015-05-20 09:07:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#issuecomment-103818286  

I have tried to address all comments made in relation to this PR.  
Major changes to look at:  
1. I have introduced a new algorithm `normalize()`/`return_normalized<>()` in `detail` namespace. This algoriothm takes care of the normalization. `normalize()` works for points and boxes right now.  
2. I have introduced another new algorithm `convert_units()`; it is responsible for changing the units of a point or a box from degrees to radians or vice versa.  
3. Both `envelope` and `expand` have been re-implemented to use these two algorithms. The code should be clearer now (I have to admit I like it much better).  
  
Things to worry about: `envelope` and `expand` may not yet work with points in the spherical equatorial coordinate system that have dimension 3 (longitude, latitude and height for example). I will do this as part of another PR. Having said that, `equals` now works correctly with such points (this is partly the reason for the major changes in commit ba93049 and for the additional test cases added in commit 5ecc576).  
  
Finally, I have not removed the latitude normalization code as suggested in the comments of PR #271, but rather introduced the macro `BOOST_GEOMETRY_NORMALIZE_LATITUDE`. I find this a better solution, in the sense that some user may eventually make use of it. In any case, since the latitude normalization code is guarded by this macro, it does not really hurt to have it in there.  
  
At this point I would like to ask for a second round of review.

---

## Comment 73

> Username: awulkiew  
> Created_at: 2015-05-20 17:53:19 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30729491  

This functionality could be moved (now or later) somewhere into the `detail::assign` and/or changed into a tool for assigning some subset of coordinates. I recal that I've seen something like this already in the library but I was unable to find it now.

---

## Comment 74

> Username: awulkiew  
> Created_at: 2015-05-20 22:16:18 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30755508  

Typically in Geometry similar functions (`convert`, `transform`) has different semantics, i.e. they take two parameters - input and output Geometries. In general the current use case seems to enforce "erroreous" behavior. AFAIU currently this function can be used to convert the coordinates of some Geometry from one unit to another. This means that at some point the new coordinates are calculated and in some cases this geometry contains coordinates in wrong units. Isn't it confusing? Wouldn't it be more clear if a geometry with specific CS and units was used internally and then at the end converted to the output Geometry if needed?

---

## Comment 75

> Username: mkaravel  
> Created_at: 2015-05-21 05:25:10 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30773678  

Having a general tool for assigning a subset of the coordinates also crossed my mind (as a tool that I would like to have available). I agree with you, it would be nice to have such a tool.  
I suggest postponing the move of `assign_loop` to `detail::assign` for another PR, and focus right now on the design of this PR.

---

## Comment 76

> Username: mkaravel  
> Created_at: 2015-05-21 05:32:48 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30773889  

I do not fully understand why the behavior is erroneous. I have a geometry whose coordinates were computed and assigned somehow. Now I want to change the units to something else (because the units were wrong in the first place, or because I want to convert this geometry to another one with different units). This is what `convert_units()` does.  
  
Even if we follow your suggestion and use geometries with specific units internally (which I do not want to force actually), we would still need a tool like `convert_units()`, since `convert()` does not change units. What I could use is `bg::transform()`. I will investigate this modify the code appropriately.

---

## Comment 77

> Username: awulkiew  
> Created_at: 2015-05-21 11:00:51 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30791796  

Yes, it's also my understanding that we need a tool other than `convert()`. I'm complaining about the name, parameters and semantics in general. In Geometry functions that converts or transforms one geometry into another takes 2 parameters. This function corrects the geometry, not converts (in Geometry sense) so a better name would be `correct_units()`. Then the 2 additional template parameters defining the units doesn't fit. At least one of them isn't needed since the Geometry is known. But for now we could leave it as it is since I don't have a better idea right now, it's a detail after all. Or do you have?  
  
EDIT: by saying "leave it as it is" I had in mind still renaming it to `correct_units()` but leaving the parameters as they are now.

---

## Comment 78

> Username: mkaravel  
> Created_at: 2015-05-21 11:06:28 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30792058  

I am trying `bg::transform()`. It seems to work right now, with the "drawback" that I have to define some intermediate helper geometries (that have the correct units).  
  
I will post more once I am done testing the transform approach.

---

## Comment 79

> Username: mkaravel  
> Created_at: 2015-05-22 06:55:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#issuecomment-104541134  

I have removed the `convert_units<>` utility; the change in coordinate system units is now done using `bg::transform()`. See commit 144fca3 for the details.

---

## Comment 80

> Username: awulkiew  
> Created_at: 2015-05-22 11:19:17 UTC  
> Updated_at: 2015-05-22 21:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30887226  

Please put a comment here explaining why is this specialization defined like this.

---

## Comment 81

> Username: mkaravel  
> Created_at: 2015-05-22 22:47:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#discussion_r30936291  

Done. See commit b0290dd.

---

## Comment 82

> Username: awulkiew  
> Created_at: 2015-05-26 14:37:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#issuecomment-105546859  

Thanks! I'm ok with merging.

---

## Comment 83

> Username: mkaravel  
> Created_at: 2015-05-28 09:36:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#issuecomment-106248640  

@barendgehrels Are you okay with merging too?

---

## Comment 84

> Username: barendgehrels  
> Created_at: 2015-05-28 18:00:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/289#issuecomment-106533425  

Sure!

---
