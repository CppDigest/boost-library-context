# #302 Envelope and expand for spherical coordinate systems [Merged]

> Username: mkaravel  
> Created at: 2015-06-01 21:24:43 UTC  
> Updated at: 2015-07-10 04:42:16 UTC  
> Merged at: 2015-07-10 04:42:16 UTC  
> Closed at: 2015-07-10 04:42:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/302  

This PR completes the work started in PR #289, by adding supporting for `bg::envelope()` and `bg::expand()` for geometries that have dimension greater than 2.  
  
A new/different design approach was taken: the dispatch class for both envelope and expand algorithms now takes a range of dimensions as template parameters (the semantics are that the envelope is computed, or the expansion is performed, only for the dimensions in the passed range).   
  
This new design has allowed for an-easier-to-follow dimension-centric implementation for the envelope and expand algorithms, and natively allows for support for computing the envelope and for performing expansion of geometries with dimension greater than 2 (such as geometries that also accommodate height information).

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-06-10 22:17:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#issuecomment-110934346  

Commit 1a4d250 changes the behavior of `bg::envelope()` for polygons: if a polygon has empty exterior ring, then its interior rings are considered. This obviously applies to invalid polygons only.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-06-10 22:21:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#issuecomment-110935054  

Commit a38f7bf initializes the MBR in the same way as if `bg::assign_inverse(mbr)` was called.  
This behavior is consistent with `bg::envelope()`'s behavior in boost 1.58 (and earlier), and has been (temporarily) restored so as to avoid any possible regressions between 1.58 and 1.59.  
  
The initialization of the MBR may change in a later boost release, as such an initialization of the MBR depends on `std::numeric_limits<>::max()` which is not suitable for user-defined number types.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-06-21 20:56:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#issuecomment-113956336  

I need feedback about this PR. I would like to have it merged as part of boost 1.59, and time, I feel, it running out.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2015-07-02 13:51:38 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33779188  

Do I understand correctly that `transform_units()` converts first two coordinates?  
So a specialization for `Dimension=0` transforms degrees/radians of the first 2 coordinates and then somehow else transforms the remaining coordinates?  
Is there a possibility to instantiate this template with `Dimension==1`?  
Anyway, normally in Geometry the templates and specializations taking `Dimension` handles this specific `Dimension`. If more dimensions must be handled some utilities/tools are used in a bigger algorithm. So AFAIU this is not consistent with the way how in general we write algorithms.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2015-07-02 14:08:35 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33781032  

In general, such templates taking the `Dimension` in Boost.Geometry works specifically on this Dimension. The way how the algorithm is designed and divided into templates is confusing for me. I also don't see a reason why the algorithm is separated into the `envelope_box` and `envelope_indexed_box` both dispatched with `Dimension`. It makes very simple thing quite complicated. And this simple thing is:  
  
```  
1. for cartesian:  
    1.1. expand_coordinates<0, DimCount>(...)  
2. for spherical and geographic:  
    2.1. normalize_coordinates_0_and_1()  
    2.2. transform_units<0, 2>()  
    2.3. expand_coordinates<2, DimCount>(...)  
```  
  
The dispatching with `Dimension` is needed only on the `expand_coordinates` step. The `envelope_box` must be only dispatched with CSTag. If in specializations done for coordinate systems those such utilities as mentioned above were used it'd be clear what's happening here. Those utilities could still be reusable, taking ranges of dimensions.  
  
Or am I missing something and there is some usage I don't see?

---

## Comment 6

> Username: awulkiew  
> Created_at: 2015-07-02 14:26:20 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33782887  

Why is the `envelope_polygon` dispatched with `Dimension`? Does it mean that this template may be instantiated for various Dimensions? If that was true then it would mean that the loop in `envelope_range` would be there in the code several times (one for each Dimension). In it was not true and always a specific Dimension was passed here then what's the purpose of those parameters?

---

## Comment 7

> Username: awulkiew  
> Created_at: 2015-07-02 14:29:01 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33783145  

This is not fullt backward compatible with the `assign_inverse()`. There `boost::numeric::bounds<T>::lowest()` and `boost::numeric::bounds<T>::highest()` are used.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2015-07-02 14:32:24 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33783513  

What's the advantage of implementing of your own version of `initialize` instead of just using `assign_inverse()` and in case if it wasn't working the right way modifying it approprietly? E.g. by checking if the `lowest`/`highest` are different than 0 and if not setting `1` and `-1` to min and max corners coordinates accordingly?

---

## Comment 9

> Username: awulkiew  
> Created_at: 2015-07-02 15:04:34 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33786936  

Ok I see the purpose of having the Dimensions here. First the segments must be handled for the first 2 coordinates and then for the rest of the coordinates points can be used as before. Still I think that it shouldn't be done on the `envelope` level. Mainly because of the "obscured" handling of coordinates if Dimension is 0.  
  
Btw, AFAIU this algorithm is called for both Linestrings and MultiLinestrings. And for both of those geometries the Segments are gathered in a separate container (~~which is twice as big as the initial Geometry!~~ EDIT: not true, only the pairs of longitudes are stored). Correct me if I'm wrong but must we search for gaps using Segments? I think that with Single geometry it's clear what is the Box because the Geometry is topologically closed, and the segments between two arbitrary points on a sphere are well defined (the shortest lines). So for a Single geometry you just need to traverse the segments and expand the Box. Only for MultiGeometry the gaps must be found, and the intervals are then the longitudes of envelopes of SingleGeometries, not Segments. This reduces the complexity significantly.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2015-07-02 15:10:28 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33787647  

In this case which dimensions exactly are handled in this `envelope` call? `0` and `1` or `0` to `DimensionCount`?

---

## Comment 11

> Username: awulkiew  
> Created_at: 2015-07-02 15:28:27 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33789572  

The problem may be indeed the Segments going though the pole so having `longitude[i+1] == longitude[i] + 180` because then we don't know if the Box should be expanded left or right. Btw, how are such Segments handled now? If the intervals of Segments are gathered first then I'm guessing that there is only one possible representation. But this representation of Segment's Envelope may be not correct for the Linestring since the complementary Box could be better, on the opposite side of the globe.

---

## Comment 12

> Username: awulkiew  
> Created_at: 2015-07-02 15:34:06 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33790161  

I forgot about the most important thing. Please treat my comments about the possible optimizations and improvements as general remarks which may be postponed, since those things are not parts of this PR.

---

## Comment 13

> Username: mkaravel  
> Created_at: 2015-07-02 19:25:10 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33813496  

Yes, your understanding is correct.  
A specialization for `Dimension` equal to `0` indeed transforms degrees/radians for the first 2 coordinates and does nothing for the remaining ones.  
Yes, in theory you can instantiate it with `Dimension` equal to `1`. This is never done by the envelope code for non-Cartesian coordinate systems. If instantiated with `1`, then it behaves as if the coordinates where Cartesian.  
  
I do not agree with your consistency argument. I do agree that it is not that common, but it does exist. We do have in several places a dimension range for which the algorithm/utility is implemented. It is the same here: there are two template parameters: `Dimension` and `DimensionCount`; the algorithm semantically deals with all coordinates within this dimension range.

---

## Comment 14

> Username: mkaravel  
> Created_at: 2015-07-02 19:29:46 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33813915  

I will change these. Thanks for pointing the differences.

---

## Comment 15

> Username: mkaravel  
> Created_at: 2015-07-02 19:34:03 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33814306  

I agree that we could use `assign_inverse()`.  
  
I decided to have a separate initialization procedure in order to be more generic. My design allows for possibly other kinds of initialization, different from what `assign_inverse()` does (`assign_inverse()` has very clear semantics: the min coordinate has higher value than the max coordinate).  
  
If there is a clear consensus that we prefer `assign_inverse()` then I am okay with using that. BTW, and this is not part of this PR, `assign_inverse()` should properly treat UDTs. Right now it returns a box that degenerates to the origin, and this is far from being correct.

---

## Comment 16

> Username: mkaravel  
> Created_at: 2015-07-02 19:36:52 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33814556  

There is no need to expand here: we have a box and we return a box. Assuming that the input box is valid, the only thing that possibly needs to be done is normalization and unit transformation. The rest are just conversions.

---

## Comment 17

> Username: mkaravel  
> Created_at: 2015-07-02 19:38:53 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33814739  

I have not yet implemented support for polygons in the spherical equatorial coordinate system, so it is hard to see where this goes. The basic idea is that for the spherical equatorial coordinate system, we need to compute the envelope of longitudes and latitudes, and then call the generic `envelope_polygon::apply()` method for the remaining coordinates.

---

## Comment 18

> Username: mkaravel  
> Created_at: 2015-07-02 19:39:35 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33814792  

`0` to `DimensionCount`.

---

## Comment 19

> Username: mkaravel  
> Created_at: 2015-07-02 19:45:32 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33815320  

I think I see your point here. It might be the case that `DimensionCount` should be replaced by `2` here.  
I will double-check it.

---

## Comment 20

> Username: mkaravel  
> Created_at: 2015-07-02 19:48:15 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33815585  

I will get back to these comments a bit later, after processing your comments.

---

## Comment 21

> Username: awulkiew  
> Created_at: 2015-07-02 20:18:30 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33818236  

If the purpose of this specialization is to modify all of the coordinates then why is it dispatched with `Dimension`?  
  
What utilities do you have in mind? My point is that more than one dimension is handled in a specialization for `Dimension==0`.  
  
My question regarding the problem with `Dimension==1` was about the code similar to this:  
  
```  
envelope_indexed_box<Index, 1, DimensionCount, spherical_equatorial_tag>  
```  
  
AFAIU here the generic version would be instantiated. I agree that probably 1 is never passed into this template, but the templates parameters suggest that it might be.

---

## Comment 22

> Username: awulkiew  
> Created_at: 2015-07-02 20:27:01 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33819235  

Right, this is envelope of a box, so just replace `expand` with `assign`. My point is that the algorithm is well defined in each coordiante system but for the reason unknown to me it's dispatched with `Dimension`. And requires that for non-cartesian CS this `Dimension` should be set to `0`. E.g. would it have sense to call `envelope_box<2, 3, geographic_tag>`? Is it used anywhere? Why isn't it just dispatched with CSTag?

---

## Comment 23

> Username: awulkiew  
> Created_at: 2015-07-02 20:30:58 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33819587  

Do you have an idea how a Box could be initialized differently? Also taking into account that it was initialized like this in the past.

---

## Comment 24

> Username: awulkiew  
> Created_at: 2015-07-02 20:47:58 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33821151  

Yes, the example usage can be found in the version for Linear right? I see that this design is generic and I of course like it. I'm just saying that it's slightly confusing because some things have 2 purposes instead of 1. E.g. `disjoint::envelope` and logically "derived" details like `envelope_box` or `envelope_polygon` in your design is used to disptch with both `Dimension` and `CSTag`. Those purposes are not compatible because the 2 first coordinates are handled in the same time in non-cartesian CSes. Therefore IMO `envelope` is not a good place for dispatching with `Dimension`. I'd rather implement all tools working on dimensions ranges deeper and use them in `disjoint::envelope` dispatched only with CS.

---

## Comment 25

> Username: awulkiew  
> Created_at: 2015-07-02 21:00:14 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33822395  

Actually, maybe not? If here all of the coordinates were handled then all of the coordinates of the resulting envelope could be updated here, inside this loop. Then it wouldn't be needed to call the `dispatch::envelope` for the dimensions > 2 and therefore have 2 loops instead of 1. Or am I missing something?

---

## Comment 26

> Username: mkaravel  
> Created_at: 2015-07-02 21:32:50 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33825095  

Done. See commit bfe3279.

---

## Comment 27

> Username: mkaravel  
> Created_at: 2015-07-02 21:50:08 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33826443  

The reason there is no specialization for `Dimension==1` is because of the normalization.  
In fact since the normalization normalizes longitudes only, and since latitudes are assumed to be in `[-90,90]` anyway, then the generic version for `1` would work correctly. So there is no need to have a separate specialization for `Dimension` equal to `1`.

---

## Comment 28

> Username: mkaravel  
> Created_at: 2015-07-02 21:56:10 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33826827  

You could certainly call `envelope_box<2, 3, geographic_tag>` it would simply convert the coordinate of dimension 2.  
If I understand your question correctly, the reason for dispatch using dimensions as well is due to the new general dispatch design for envelope, that also takes a dimension range into account. I agree that it might make less sense for `envelope_box`, but on the other hand this is the general dispatch design, and I thought it would be better to follow it globally, for uniformity.

---

## Comment 29

> Username: mkaravel  
> Created_at: 2015-07-02 22:00:04 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33827091  

I am afraid do it the way you suggest would greatly increase the code size.  
I thought that dispatching with respect to both dimension and coordinate system is not that complicated. In `bg::distance()` we already dispatch over multiple types at the same time...

---

## Comment 30

> Username: mkaravel  
> Created_at: 2015-07-02 22:02:28 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33827265  

One possibility is to give the value `0` to both min and max values (I do not like it actually, but it is a possibility).  
Another option would be to assign `1` to the min coordinate value and `-1` to the max coordinate value (this would actually be my personal preference). It is true that this could be done at the `assign_inverse()` level, but this would make `assign_inverse()` backward incompatible, which I do not like.

---

## Comment 31

> Username: awulkiew  
> Created_at: 2015-07-02 22:14:39 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33828188  

Well, AFAIU for Dimension == 1 the default version would be instantiated so the units won't be transformed.  
  
Please be aware that this is only an example, a manifestation of a problem I see that this dispatching with Dimension is synthetic since here for spherical CS all of the operations should always be performed, for all dimensions. As I see it, now the Dimension is there but still in the same time it's required that it must be set to 0. It's because the dispatching with Dimension is not really done here, it's an illusion, so not required.

---

## Comment 32

> Username: awulkiew  
> Created_at: 2015-07-02 22:17:24 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33828376  

But are the algorithms dispatched with Dimension in the same time? I don't recall anything like this.

---

## Comment 33

> Username: mkaravel  
> Created_at: 2015-07-02 22:21:22 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33828667  

You are right. The default version would not transform units. This is indeed a "problem".  
  
I am still not sure what see as a problem here (besides the special treatment of `Dimension==1`). The idea is the following: given `Dimension` and `DimensionCount`, the code here computes the envelope for all coordinates from `Dimension` (inclusive) to `DimensionCount` (not inclusive). When `Dimension==0` and the coordinate system is the spherical equatorial or geographic one, there exists a specialization. The specialization by itself does not mean that things will be computed many times....

---

## Comment 34

> Username: awulkiew  
> Created_at: 2015-07-02 22:24:05 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33828816  

And here only the `envelope()` is made backward incompatible. `assign_inverse()` was always related with the boxes, and in particular how the Boxes may be expanded. I'd say that it'd be more consistent if `envelope()` worked the same as `assign_inverse()` and `expand()`. But ok, I'm not against the custom `initialize` specifically for the purpose of envelope, esspecially that we plan to deprecate `assign_inverse()`.

---

## Comment 35

> Username: mkaravel  
> Created_at: 2015-07-02 22:25:33 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33828927  

You are right. Either here I would need to concentrate on the first two dimensions, or keep things as they are and omit the dispatch call at the end.

---

## Comment 36

> Username: mkaravel  
> Created_at: 2015-07-02 22:25:44 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33828941  

I will update the code accordingly.

---

## Comment 37

> Username: mkaravel  
> Created_at: 2015-07-02 22:31:18 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33829223  

In distance we do dispatching with respect to geometry types and strategies at the same time. Not distance (not yet at least as we still work on 2D).

---

## Comment 38

> Username: awulkiew  
> Created_at: 2015-07-03 10:54:16 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33855715  

This would be the dispatching by DimensionCount, not by Dimension. The DimensionCount is consistent with the CSTag. For 3D the algorithm could be different, the DimensionCount parameter would be used to pick the right algorithm implemented internally. The Dimension on the other hand is a part of the meta-algorithm.

---

## Comment 39

> Username: awulkiew  
> Created_at: 2015-07-03 11:30:49 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33857509  

Exactly, in spherical and geographic CS the `Dimension` must be `0` otherwise the template will not work as intended. I understand that this follows the overall design, and that this template is consistent with the rest. This may be an indicator that something is wrong with the whole design.  
  
I don't know how do the others see it but I see it like this. Let's consider a function:  
  
```  
// \param p1   it may be any number but if p2 is 2 or 3 then p1 must be 0  
// \param p2   it must be 1, 2 or 3  
do_something(p1, p2);  
```  
  
Do you like the design of this function?  
The problem is that the parameters are not orthogonal. It's more general actually, it's about a well-defined purpose, defined by the parameters (see e.g. why is `realloc()` considered as a badly designed function). What is true for functions is also true for meta-functions. In the current design the specializations has 2 purposes instead of one. They in the same time pick up the correct algorithm for the CS and implements this algorithm. Actually this would be a better example:  
  
```  
// \param first  the iterator pointing to the first element of a Range  
//               if the pred is Abc or Def then first must be the "true"  
//               first element of a Range.  
// \note    Have in mind that you should always pass the whole Range here.  
for_each_something(first, last, pred)  
```  
  
It's even more complicated because it's recursive. Maybe a recursive function traversing some tree but for some parameters requireing that you start at the root and for other parameters don't requireing it but then ignoring this other parameter would be a better example.  
  
And I'm not saying that you should support all of the special cases. It doens't have sense because always the same parameters are passed into these templates. I'm saying that for me the design is overcomplicated.

---

## Comment 40

> Username: mkaravel  
> Created_at: 2015-07-03 16:37:38 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r33873576  

Your arguments are very nice and correct, but I am afraid somehow irrelevant.  
  
Unlike functions, in templates we have partial specialization, which in my mind is the same as being able to define the value of a meta-function for a specific input value.  
  
So what is the general paradigm used here (I have abbreviated some names in order to write less):  
  
```  
template <typename Geom, std::size_t Dim, std::size_t DimCount, typename CS_Tag>  
struct envelope_algo_implementation  
{  
    // algo goes here  
};  
  
template <typename Geom, typename DimensionCount>  
struct envelope_algo_implementation<Geom, 0, DimensionCount, spherical_equatorial_tag>  
{  
    static inline return_type apply(...)  
    {  
  
        // do something special for dimensions 0 and 1 and then call the generic version  
        envelope_algo_implementation  
            <  
                Geom, 2, DimensionCount, spherical_equatorial_tag  
            >::apply(...);  
    }  
};  
```  
  
This is the same as having a function that internally does the following check:  
  
```  
if (dimension == 0 && tag == spherical_equatorial_tag)  
{  
    // do something different/special  
}  
```  
  
According to the design I have followed, I simply indicate how `envelope_algo_implementation` is to be implemented for specific values of template arguments. I do not really see the same dependencies as the ones you propose.  
  
Of course it is indeed possible to move the dependence on `Dimension` one layer down. I personally think it is not necessary, but if this is the preferred choice, I can certainly do it that way.  
  
**_Note:**_ if we remove the normalization part, then some parts of the envelope code will have to be updated and the design will become even more clear. Dimensions `0` and `1` will be independent from each other and the per-dimension design will make much more sense I think.

---

## Comment 41

> Username: mkaravel  
> Created_at: 2015-07-07 01:38:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#issuecomment-119042541  

@awulkiew In commits b376567 and fc7ed8f (and partially in ba8bc96) I have addressed all your comments.  
  
In particular, `Dimension` and `DimensionCount` are now gone from the dispatch classes for `bg::envelope` and `bg::expand`, and the implementation for linestrings and multilinestrings is now performed as you suggested.

---

## Comment 42

> Username: barendgehrels  
> Created_at: 2015-07-08 11:26:02 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34137764  

The license (last 2 lines) in this PR has two versions (sometimes 3 lines).  
But apparently somehow we changed the license from a 3-line version to this 2-line version. Because the 3-lines version occurs 825 times, in our code the 2-lined version < 100 times.  
  
This version is incorrect because it refers to the license guidelines, and not to the license itself.  
  
I just checked the Boost page and they (now) recommend yet another version  
http://www.boost.org/users/license.html  
  
So we should adapt our code to have only one version, and the commended one (I'm only talking about the last two lines, not about the rest)  
  
So it now should read:  
`// Distributed under the Boost Software License, Version 1.0.`  
`//    (See accompanying file LICENSE_1_0.txt or copy at`  
`//          http://www.boost.org/LICENSE_1_0.txt)`

---

## Comment 43

> Username: barendgehrels  
> Created_at: 2015-07-08 11:27:29 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34137845  

In all new files, these two lines (referring to Geodan) can be omitted. We should only keep them in the files which are really redesigned from the old library.

---

## Comment 44

> Username: barendgehrels  
> Created_at: 2015-07-08 11:27:47 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34137859  

Oops, extra space!

---

## Comment 45

> Username: barendgehrels  
> Created_at: 2015-07-08 11:33:01 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34138116  

This two loops look too complex.  
You can easily just walk through it with a normal for-loop, and add one boolean variable `initialized` to check if you should calculate the initial envelope or expand it.  
You can then also move the generic initialize to the end, when it is not yet initialized.

---

## Comment 46

> Username: barendgehrels  
> Created_at: 2015-07-08 11:34:16 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34138177  

this looks better (single loop).  
You could add `else { initialize<...> }` instead of the initialization above.

---

## Comment 47

> Username: barendgehrels  
> Created_at: 2015-07-08 11:35:42 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34138255  

Why, by the way, is the step via boxes necessary? Is this for periodic longitude or are there other reasons too? Could you put a short comment with the reason here?

---

## Comment 48

> Username: barendgehrels  
> Created_at: 2015-07-08 11:41:00 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34138548  

Why is all this? bg::wkt works for every geometry...

---

## Comment 49

> Username: barendgehrels  
> Created_at: 2015-07-08 11:44:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#issuecomment-119547826  

Thanks for the PR! I'm OK with merging (after addressing comments).

---

## Comment 50

> Username: awulkiew  
> Created_at: 2015-07-08 11:59:47 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34139687  

This could be more generic tool, allowing to specify the Dimension and maybe also the CoordinateSystem though the latter probably doesn't have much sense.  
  
I see that it's used only in `transform_units_impl`. I'm curious why did you decide to implement the view instead of just putting simpler code there? Do you think that it could be useful somewhere else?

---

## Comment 51

> Username: awulkiew  
> Created_at: 2015-07-08 12:24:38 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34141320  

It's great that you added the test for empty geometries. It was missing before.

---

## Comment 52

> Username: awulkiew  
> Created_at: 2015-07-08 12:32:22 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34141797  

Great, so AFAIU the envelope(Linestring) has linear complexity and envelope(MultiLinestring) first gathers Linestrings' MBRs and then uses them to calculate the envelope with linearitmic complexity?

---

## Comment 53

> Username: awulkiew  
> Created_at: 2015-07-08 12:33:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#issuecomment-119559270  

Thanks Menelaos for the improvements!  
I'm ok with merging after all of the issues mentioned above are addressed.

---

## Comment 54

> Username: mkaravel  
> Created_at: 2015-07-09 08:25:40 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34233183  

I have updated all `expand` and `envelope` related files to use the latest wording.  
  
In any case we should go over all files and change at least the two-line versions and eventually all of them to the latest version.

---

## Comment 55

> Username: mkaravel  
> Created_at: 2015-07-09 08:28:58 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34233395  

I have now omitted these lines in many files in `envelope` and `expand`.  
I have to admit it is not clear for me when to keep it and when not.  
  
The general guideline I was following was to keep it as long as: (1) I consider a file as originating (fully or partially) from an older one (even if the new file lies at a different location and has different name), and (2) the old file was having these two lines referring to Geodan.

---

## Comment 56

> Username: mkaravel  
> Created_at: 2015-07-09 08:29:04 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34233405  

Fixed.

---

## Comment 57

> Username: mkaravel  
> Created_at: 2015-07-09 08:29:14 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34233419  

Done as you propose.

---

## Comment 58

> Username: mkaravel  
> Created_at: 2015-07-09 08:29:38 UTC  
> Updated_at: 2015-07-09 08:33:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34233450  

Done as you propose (initialization).

---

## Comment 59

> Username: mkaravel  
> Created_at: 2015-07-09 08:33:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34233714  

Yes, it is because of the periodicity of longitudes.  
The comments for this part of the code have been updated.

---

## Comment 60

> Username: mkaravel  
> Created_at: 2015-07-09 08:39:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34234088  

`bg::wkt` outputs boxes as polygons. I do not particularly like this for two reasons:  
1. In debug mode, or when an error occurs, I prefer to see the word `BOX` instead of `POLYGON` in the output (it makes it easier for me to register that the involved geometry is a box).  
2. In the spherical equatorial system, a box is not really a polygon (whose segments are geodesics on the sphere), but rather a min and a max point indicating the minimum and maximum longitude and latitude coordinates of a geometry.

---

## Comment 61

> Username: mkaravel  
> Created_at: 2015-07-09 08:44:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34234481  

Yes, it could be more generic, and if needed we can change the existing implementation.  
  
You are right, it is used only in `transform_units_impl`. I felt that having a view is a better design, makes the code more clear, and more re-usable.  
  
I have no particular other use for it right now in any other place, but if, for example, at some point we want to make `bg::transform` transform units for points that are not 2-dimensional, then this can immediately be used.  
  
Finally, what you refer to as "simpler code" might not be that simpler after all, or as nice, and certainly less re-usable.

---

## Comment 62

> Username: mkaravel  
> Created_at: 2015-07-09 08:46:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#discussion_r34234689  

Yes, you are right.  
  
Currently `envelope(Linestring)` has _O(N)_ complexity (_N_ is the number of points in the linestring).  
  
Also `envelope(MultiLinestring)` has _O(N+L log(L))_ complexity where _N_ is the total number of points in the multilinestring and _L_ and number of linestrings in the multilinestring.

---

## Comment 63

> Username: mkaravel  
> Created_at: 2015-07-09 08:47:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/302#issuecomment-119878155  

@awulkiew @barendgehrels Thank you both for your comments.  
I will wait for a day until I merge, so that you have the time to respond/react if needed.  
  
@barendgehrels Once this PR is merged, could you possibly merge develop into master once more?

---
