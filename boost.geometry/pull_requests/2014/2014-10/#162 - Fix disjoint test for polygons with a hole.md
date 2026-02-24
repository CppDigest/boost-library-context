# #162 Fix disjoint test for polygons with a hole [Closed]

> Username: zerebubuth  
> Created at: 2014-10-16 17:17:09 UTC  
> Updated at: 2014-10-18 00:06:04 UTC  
> Closed at: 2014-10-18 00:06:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/162  

This is a follow-on from #159, but against the `develop` branch and with some extra fixes which were brought to light by the extra tests which are in the `develop` branch, namely:  
- Multipolygons: Tests were failing when the second part of a multipolygon intersected, as `point_on_border` returns only a single point. The check now tests all parts of a multipolygon.  
- Touching points: Previously, it was checking if a point on one geometry was within the other, but this was not sufficient if the outer rings touched. The within check would fail, as the point may be on the boundary rather than strictly within. The code now uses `covered_by` and passes the tests.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-10-16 19:15:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#discussion_r18979139  

If I understand correctly, we avoid forward declarations in Boost.Geometry.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-10-16 19:18:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#discussion_r18979318  

The brace should go in the following line:  
  
```  
namespace dispatch_multi  
{  
```  
  
Also, such a namespace (performing dispatching within a `detail` namespace), if very untypical.  
I would call it `detail_dispatch`, rather than `dispatch_multi`.  
To be honest, I do not really like the way `rings_containing` is implemented. Please also see other comments (to be written).

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-10-16 19:19:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#discussion_r18979359  

We put `const` after the type: `SecondGeometry const`

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-10-16 19:21:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#discussion_r18979498  

We typically put `static` before `inline`: `static inline bool apply(`

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-10-16 19:28:08 UTC  
> Updated_at: 2014-10-16 19:28:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#discussion_r18979939  

Personally, I would put here the default argument for the `SecondGeomTag`, and remove entirely the third template parameter in the `dispatch_multi::rings_containing` class.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2014-10-16 19:29:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#discussion_r18980062  

Since this is a specialization where the second geometry is a multipolygon, I would name the second template parameter as `MultiPolygon` instead of `SecondGeometry`. I think this would make it even more clear.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2014-10-16 19:32:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#discussion_r18980220  

Here you could re-write the conditional expression as:  
  
```  
rings_containing_impl  
    <  
        FirstGeometry,  
        typename boost::range_value<SecondGeometry>::type  
    >::apply(geometry1, *it)  
```  
  
I am assuming in the code above that you have also added the default value for the third template argument as suggested in another comment.  
The advantage of this approach, is that you no longer need the forward declaration for `detail::disjoint::rings_containing`.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2014-10-16 19:34:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#discussion_r18980374  

Check out `boost/geometry/algorithms/detail/check_iterator_range.hpp`. I believe you can use that to implement the for-loop above.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2014-10-16 19:39:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#discussion_r18980722  

I don't particularly like the idea of having the `rings_containing_impl` class.  
I believe you can implement what you want with just the `ring_containing` class you have here.  
Just write the general algorithm here and specialize it for multi-polygons.  
  
Given this remark, my other comment about the conditional expression should be re-written as:  
  
```  
rings_containing  
    <  
        FirstGeometry,  
        typename boost::range_value<SecondGeometry>::type  
    >::apply(geometry1, *it)  
```

---

## Comment 10

> Username: mkaravel  
> Created_at: 2014-10-16 19:41:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#discussion_r18980843  

If you follow the suggestion below regarding how to implement `rings_containing` in this namespace, this comment is irrelevant.

---

## Comment 11

> Username: mkaravel  
> Created_at: 2014-10-16 19:43:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#issuecomment-59418555  

@zerebubuth Hi Matt. Thanks for doing this. I hope my line comments will be helpful.

---

## Comment 12

> Username: mkaravel  
> Created_at: 2014-10-16 19:55:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#discussion_r18981798  

Let me be more precise about this.  
My suggestion is to write an inner class as follows:  
  
```  
struct unary_rings_containing_predicate  
{  
    unary_rings_containing_predicate(FirstGeometry const& geometry1)  
        : m_geometry1(geometry1)  
    {}  
  
    template <typename Geometry2>  
    static inline bool apply(Geometry2 const& geometry2)  
    {  
        return rings_containing<Geometry1, Geometry2>::apply(m_geometry1, geometry2);  
    }  
  
    Geometry1 const& m_geometry1;  
};  
```  
  
Then instead of the for-loop you can write:  
  
```  
return check_iterator_range  
    <  
        unary_rings_containing_predicate  
    >::apply(boost::begin(geometry2),  
             boost::end(geometry2),  
             unary_rings_containing_predicate(geometry1));  
```

---

## Comment 13

> Username: mkaravel  
> Created_at: 2014-10-16 19:58:32 UTC  
> Updated_at: 2014-10-16 19:58:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#discussion_r18982000  

You need to add an include for Boost.Range. So above the first include add:  
  
```  
#include <boost/range.hpp>  
```  
  
We put includes of other boost libraries above BG includes.

---

## Comment 14

> Username: mkaravel  
> Created_at: 2014-10-16 20:09:58 UTC  
> Updated_at: 2014-10-16 20:10:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#discussion_r18982793  

I double-checked the implementation of `check_iterator_range`.  
You probably need to make the inner class return ~~the~~ what I wrote above negated, and also negate the result of `check_iterator_range` upon return.

---

## Comment 15

> Username: awulkiew  
> Created_at: 2014-10-16 20:48:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#issuecomment-59427876  

Ok, so this is called also for MultiPolygons and Barend's argument was valid.  
  
Why didn't you just replace `point_on_surface()` with `point_on_border()` and `within()` with `covered_by()` in the original code? `for_each_ring` should handle MultiPolygons. In the `check_each_ring_for_within::apply()`:  
  
```  
typename point_type<Range>::type pt;  
if ( geometry::point_on_border(pt, range) )  
{  
    if ( geometry::covered_by(pt, m_geometry) )  
    {  
        has_within = true;  
    }  
}  
```  
  
It passes all tests.  
  
Btw, may replacement of `within()` with `covered_by()` cause any problems? I'm guessing that it could give different results for some disjoint Polygons very close to each other, than the version using `within()` - also no intersection points but `covered_by()` could return true. This is another place where non-EPS/0-EPS `within()` could be useful.

---

## Comment 16

> Username: zerebubuth  
> Created_at: 2014-10-17 14:28:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#issuecomment-59520687  

@mkaravel - Thanks for the feedback!  
  
@awulkiew - Okay, sounds like I've overcomplicated the patch. Do you want me to close out this PR so that you can submit one using `check_each_ring_for_within`?

---

## Comment 17

> Username: awulkiew  
> Created_at: 2014-10-17 19:52:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#issuecomment-59565918  

@zerebubuth This depends on you. Would you like to prepare one more PR or rather leave this fix to us? Have in mind that it should be there sooner than later since the master branch is closed for bug fixes at Monday. So if the PR won't be ready for merging tomorrow I'll prepare the fix myself.

---

## Comment 18

> Username: zerebubuth  
> Created_at: 2014-10-18 00:06:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/162#issuecomment-59591107  

@awulkiew Since it's time-sensitive, it would probably be better for you to do it. I'm grateful for the effort that everyone has put into reviewing my patches, but that effort could probably be better spent elsewhere right now. :smile:

---
