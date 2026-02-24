# #303 New algorithm: is_empty [Merged]

> Username: mkaravel  
> Created at: 2015-06-02 22:14:46 UTC  
> Updated at: 2015-06-03 12:59:58 UTC  
> Merged at: 2015-06-03 12:46:55 UTC  
> Closed at: 2015-06-03 12:46:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/303  

This PR introduces a new algorithm `bg::is_empty()`. It takes as input a geometry and returns `true` if the geometry represents the empty set, `false` otherwise.  
  
**_Motivation:**_ This function is meant to replace instances of boolean expressions of the form `bg::num_points(g) == 0`, with something more efficient (and more readable). The new algorithm takes constant time, whereas `num_points()` can take linear time in the worst case.  
  
I am going to try to replace instances of the conditional expression mentioned above as part of another PR, once this PR gets reviewed and merged.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-06-03 07:23:33 UTC  
> Updated_at: 2015-06-03 12:44:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#discussion_r31599104  

We could consider checking the interior rings as well. The purpose would be to have consistent `num_points()` and `is_empty()` functions. So to maintain a correspondence similar to the one between `size()` and `empty()` functions where (`empty() == (size() == 0)`). Another way of handling it would be to modify the `num_points()` function to not check the interior rings if the exterior ring was empty.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-06-03 07:52:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#issuecomment-108235197  

I like the proposal.  
  
I don't think the performance really improves, because (besides polygon) all ranges are checked, it is a minor improvement above just calling the size. Anyway that is a minor detail. It will be indeed more clear.  
  
There are several approaches.   
1) it is consistent with num_points. Then polygon's interior rings should indeed be checked too. Though we talk about invalid polygons then (but they can be constructed)  
2) it is as fast as possible. Then polygon is OK and for (for example) a multi-linestring the range does not have to be iterated. A multi linestring with two linestrings (both empty) is not empty. But we get the same problem for bounding box then.  
  
Because of the purpose (avoid empty box) it is convenient to select 1) indeed, as you did.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-06-03 07:55:03 UTC  
> Updated_at: 2015-06-03 12:44:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#discussion_r31600800  

Perfect, the PR is very complete including all docs, samples and tests!

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-06-03 07:57:20 UTC  
> Updated_at: 2015-06-03 12:44:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#discussion_r31600938  

Right, so this is indeed considered empty...  
That is consistent with envelope

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-06-03 07:58:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#issuecomment-108236257  

I'm OK with merging, this is a perfect submission including everything we support, also variants.  
Thanks a lot.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2015-06-03 08:18:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#issuecomment-108241791  

@barendgehrels I guess that we should have clear definitions of empty geometries and use them consistently in the library. For example if an empty Polygon must have an empty exterior ring no matter how many interior rings are there then `num_points()` should probably return 0 in that case, since the geometry would be empty. If that's a problem we could have two functions for checking the number of Points, the OGC-like and consistent with the definition of emptiness and the other one, raw-data-oriented similar to `boost::size()`.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-06-03 11:34:09 UTC  
> Updated_at: 2015-06-03 12:44:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#discussion_r31614082  

I am having seconds thoughts about this issue. I think that `is_empty()` should be equivalent to `num_points() == 0`. In that respect, I agree with Adam's proposal, and will need to change the code.  
  
The natural question would be which one to modify: `is_empty()` or `num_points()`?  
For valid geometries this question does not make much of a difference (for valid geometries `is_empty()` and `num_points() == 0` are completely equivalent even now). The issue arises in invalid geometries; the expectation here is that our code should not crash, but the behavior is undefined. Given that this is already in the space of undefined behavior, I would go for testing interior rings as well.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-06-03 11:35:31 UTC  
> Updated_at: 2015-06-03 12:44:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#discussion_r31614175  

Well, this is an artifact of the Cartesian coordinate system. In spherical equatorial and geographic we **_need**_ to consider interior rings of polygons in order to correctly compute MBRs.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-06-03 11:44:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#issuecomment-108328082  

Regarding complexity: for valid geometry it **_is**_ really worst-case constant. The fact that you see a loop in the code does not mean anything; it is there to treat invalid geometries.  
  
For a valid non-empty geometry, the first time you look at a range value of a multi-geometry you realize it is not empty and you return (constant time). For valid empty geometries (basically, `MULTIPOINT()`, `MULTILINESTRING()` and `MULTIPOLYGON()`, the size of the range to check is zero, so again the `is_empty()` algorithm returns in constant time. So I would stick and keep to the constant complexity appearing in the doc, and, again for valid geometries, `is_empty()` **_is more efficient**_ than `num_points() == 0`.

---

## Comment 10

> Username: mkaravel  
> Created_at: 2015-06-03 11:48:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#issuecomment-108331384  

#### Summary (so far)  
  
I would like to make `is_empty()` equivalent to `num_points() == 0`. I will do so by changing `polygon_is_empty` to check the interior rings as well. Some test cases would have to be updated.  
  
For valid geometries the complexity _is_ constant, for invalid geometries the complexity could be linear (but I really would not care about that, and would not advertise the behavior/complexity for invalid geometries).  
  
I will update the PR with the new behavior for polygons, and wait for approval to merge.

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2015-06-03 12:08:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#issuecomment-108347004  

I agree with checking interior rings.  
I agree with the explanation about complexity, you are completely right.  
So I agree with merging after behaviour is updated.

---

## Comment 12

> Username: mkaravel  
> Created_at: 2015-06-03 12:21:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#issuecomment-108358122  

Changes done in commits 8ce6727 and d80b351.  
In the unit test I now explicitly check that the result of `is_empty()` is the same as `num_points() == 0`.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2015-06-03 12:32:18 UTC  
> Updated_at: 2015-06-03 12:44:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#discussion_r31617505  

A side note, this could be done in a single expression:  
  
```  
return boost::empty(exterior_ring(polygon))  
    && check_interior_rings(interior_rings(polygon));  
```

---

## Comment 14

> Username: awulkiew  
> Created_at: 2015-06-03 12:33:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#issuecomment-108369333  

Thanks! I agree with merging the PR.

---

## Comment 15

> Username: mkaravel  
> Created_at: 2015-06-03 12:44:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#discussion_r31618358  

Thanks a lot Adam!  
Done in commit f7a4546.

---

## Comment 16

> Username: barendgehrels  
> Created_at: 2015-06-03 12:46:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#issuecomment-108383335  

Then I merge it, thanks again!

---

## Comment 17

> Username: mkaravel  
> Created_at: 2015-06-03 12:56:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#issuecomment-108389655  

@awulkiew @barendgehrels Thank you for the comments and review!  
@barendgehrels Thank you for merging!

---

## Comment 18

> Username: barendgehrels  
> Created_at: 2015-06-03 12:59:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/303#issuecomment-108392283  

You are welcome ;)  
And the algorithm is welcome - in buffer it's already used now ;-)

---
