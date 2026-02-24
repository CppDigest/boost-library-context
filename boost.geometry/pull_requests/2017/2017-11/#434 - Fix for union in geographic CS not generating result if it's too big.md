# #434 Fix for union in geographic CS not generating result if it's too big [Merged]

> Username: awulkiew  
> Created at: 2017-11-11 02:10:50 UTC  
> Updated at: 2017-11-30 13:32:29 UTC  
> Merged at: 2017-11-14 23:25:03 UTC  
> Closed at: 2017-11-14 23:25:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/434  

If the input non-cartesian geometries are valid the result of union may be too big, i.e. may have negative area or in other words is covering area greater than half of the globe. In such case currently `union_()` function returns empty result. This PR changes that, i.e. big geometry is returned.  
  
Consider this case:  
  
    POLYGON((16 -15, -132 -22, -56 89, 67 -29, 16 -15))  
    +  
    POLYGON((101 49, 12 40, -164 -21, 117 -61, 101 49))  
    =  
    POLYGON((-128.119 74.498, -164 -21, 117 -61, 101 49, 65.345 54.723, 67 -29, 16 -15, -132 -22, -128.119 74.498))  
  
with `(-128.119 74.498)` and `(65.345 54.723)` being 2 intersection points.  
  
This PR is related to https://github.com/boostorg/geometry/pull/424. The case there is a smaller version of the one in this PR.  
  
This PR removes positive area condition from `add_rings()` if the overlay's `OverlayType` is `overlay_union`. The condition is not needed in this case because the result contains geometries at least as big as the input and AFAIU no invalid/reversed geometries are created internally as opposed to `difference` or `intersection`. Alternatively `larger(area, 0)` could be replaced with `! equals(area, 0)` but if this condition was not met this'd mean that such small polygon was in the input and therefore probably should be passed to the output.  
  
I saw that there is also `overlay_dissolve_union` overlay type. Should the check be disabled also in this case?

---

## Comment 1

> Username: vissarion  
> Created_at: 2017-11-13 13:25:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/434#issuecomment-343918028  

Thanks, the fix looks good to me. Regarding `overlay_dissolve_union` do you have a bug involving that type?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2017-11-13 13:38:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/434#issuecomment-343921253  

@vissarion No, furthermore dissolve is an operation implemented in the extensions so not officially released.  
  
Btw, an alternative approach to handling this union issue would be to throw and exception if the result geometry was too big. But I decided to not do it because it'd require to extrace CS info from strategy somehow. Also the result would not be returned (for valid input). With this PR the user could check the result and potentially do something with it.

---

## Comment 3

> Username: vissarion  
> Created_at: 2017-11-13 13:54:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/434#issuecomment-343925303  

@awulkiew Thanks for explanation. I agree with that approach, the result is meaningful and could give some information potentially useful to a user, better than throwing an exception.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2017-11-14 21:15:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/434#issuecomment-344400921  

@barendgehrels what do you think about this?

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2017-11-14 22:58:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/434#issuecomment-344428588  

I'm OK with it. Thanks.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2017-11-30 13:32:19 UTC  
> Updated_at: 2017-11-30 13:32:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/434#issuecomment-348188676  

I decided to push a quick modification outside this PR. There are 3 possible behaviors in such cases:  
- the invalid polygon is rejected, this is the original behavior which I made the default again.  
- the `invalid_output_exception` may be thrown when BOOST_GEOMETRY_UNION_THROW_INVALID_OUTPUT_EXCEPTION is defined.  
- the invalid polygon may be returned when BOOST_GEOMETRY_UNION_RETURN_INVALID is defined.  
  
The reason for that is that if the input is invalid the union' output may contain some number of invalid polygons and I susspect that rejecting invalid parts may be a desirable in some cases and this could potentially be a breaking change for some users.

---
