# #204 Fix/is simple wrong turns detection [Merged]

> Username: mkaravel  
> Created at: 2015-01-29 21:56:06 UTC  
> Updated at: 2015-02-03 19:59:33 UTC  
> Merged at: 2015-02-02 18:54:28 UTC  
> Closed at: 2015-02-02 18:54:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/204  

This PR essentially deals with two issues:  
- Optimizing the `bg::is_simple` computation for multilinestrings. In the older implementation self turns were computed for each linestring in the multilinestring individually, and then again if needed at the multilinestring level. In the new implementation the self turns of the linestrings are not computed, while the self turns of the entire multilinestring are used to determine/decide simplicity.  
- The change in the above item conveniently deals with and fixes a bug in `bg::intersects`: `bg::intersects` cannot identify an open linestring whose one endpoint is the same as an interior point as self-intersecting (for example: `LINESTRING(4 1,10 8,4 6,4 1,10 5,10 3)`). This immediately fixes a bug in `is_simple`, which, previously to this PR, erroneously considers linestrings, like the ones described just above, as simple.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-01-29 22:19:17 UTC  
> Updated_at: 2015-02-01 09:32:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#discussion_r23808177  

The whole fix looks more like a whole rewrite (no problem).  
  
What I'm wondering about this method is: can't we make better use of the turn's properties? They have already the segment-index, they know how the intersection is made, there are fractions, arrival-information, etc, why is it still necessary to call geometry::equals here? That information should be known here... At least, for A/A those info is surely known

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-01-29 22:21:46 UTC  
> Updated_at: 2015-02-01 09:32:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#discussion_r23808377  

This struct looks really good! :-)

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-01-29 22:22:44 UTC  
> Updated_at: 2015-02-01 09:32:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#discussion_r23808451  

Same question about turn-info here: can we reuse?

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-01-29 22:23:45 UTC  
> Updated_at: 2015-02-01 09:32:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#discussion_r23808531  

I assume you are referring to the last call to `geometry::equals`. I will try to rewrite the test using segment ratios.  
  
Note that we also need to fix the bug in bg::intersects that takes a single argument. It is a bit more complicated there as we need to account for the possibility to have duplicate points at the beginning and end of the linestring.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-01-29 22:23:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#issuecomment-72116739  

I've a few questions but basically I'm in favour of merging this

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-01-29 22:25:06 UTC  
> Updated_at: 2015-02-01 09:32:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#discussion_r23808630  

Same answer: I will try to replace the call to `geometry::equals` by means of the segment ratios.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-01-29 22:26:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#issuecomment-72117074  

@barendgehrels Thanks for taking a look at it so quickly.  
I will try to use segment ratios.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-01-29 22:30:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#issuecomment-72117657  

There is general question related to `bg::is_simple`: recently I modified `bg::is_valid` to accept empty multi-geometries as valid. Should `bg::is_simple` be modified accordingly to accept empty multi-geometries as simple?

---

## Comment 9

> Username: awulkiew  
> Created_at: 2015-01-29 22:48:58 UTC  
> Updated_at: 2015-02-01 09:32:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#discussion_r23810304  

Ratios not necessarily must be compatible with points. Remember the fix I made some time ago in cart_intersect?

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2015-01-29 22:50:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#issuecomment-72120613  

Good question - don't know (yet). What does the standard say here?

---

## Comment 11

> Username: awulkiew  
> Created_at: 2015-01-29 22:51:04 UTC  
> Updated_at: 2015-02-01 09:32:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#discussion_r23810449  

And here again equals(ratio, 0) not necessarily is the same as equals(point, front)

---

## Comment 12

> Username: mkaravel  
> Created_at: 2015-01-29 23:01:58 UTC  
> Updated_at: 2015-01-30 10:15:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#issuecomment-72122300  

I could not find anything in the standard about this.  
When asking the question, I was mostly thinking in terms of consistency of our own code: if we accept empty multi-geometries as valid, why not accept them as simple as well?

---

## Comment 13

> Username: mkaravel  
> Created_at: 2015-01-29 23:04:15 UTC  
> Updated_at: 2015-02-01 09:32:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#discussion_r23811351  

@awulkiew Do you have a specific example where there is a mismatch between points and rations? It would be really helpful to have it and use it as test case.

---

## Comment 14

> Username: awulkiew  
> Created_at: 2015-01-29 23:49:05 UTC  
> Updated_at: 2015-02-01 09:32:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#discussion_r23813699  

Yes, however have in mind that it was detected only for floating-point ratios (no rational) so when rescaling is disabled. See e.g. https://github.com/boostorg/geometry/commit/eeb33369ecfc2ed8104ab5207694027a30c67f04 for the test cases and https://github.com/boostorg/geometry/commit/38389101829ed993523730ecedcd097fbf1ec70b for the fix. E.g. see the "simpler case" test in line 309, points (-2.8823529411764710 -7.7647058823529411) and (-2.8823529411764706 -7.7647058823529411) are spatially equal but when ratios are checked they're not detected as equal. It's because both points and ratios are compared using machine EPS but in both cases the actual values being compared are not compatible. E.g. ratios values depends on the lengths of segments.

---

## Comment 15

> Username: awulkiew  
> Created_at: 2015-01-29 23:52:26 UTC  
> Updated_at: 2015-02-01 09:32:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#discussion_r23813887  

Btw, if you used the new get_turn_info_ll policy you shouldn't be forced to deal with this problem since for the first and the last point of non-degenerated segment a special flag (position) is set in operation member of turn_info.

---

## Comment 16

> Username: mkaravel  
> Created_at: 2015-01-30 10:17:26 UTC  
> Updated_at: 2015-02-01 09:32:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#discussion_r23833608  

Please correct me if I am wrong: if I am to use the `get_turn_info_ll` policy, then I would need to call turns by passing the same argument twice, which means that I will get a lot more turns than what I do now. I am just saying that the way I do it is a bit more efficient.

---

## Comment 17

> Username: awulkiew  
> Created_at: 2015-01-30 13:44:18 UTC  
> Updated_at: 2015-02-01 09:32:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#discussion_r23842652  

My point is, you could pass this new policy into the `detail::self_get_turn_points::get_turns<>::apply()`. Here in the implementation of 1-argument `intersects()` https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/intersects.hpp in the lines 57 and 65 the regular (A/A) turn_info and policy types are defined. But you could use the turn_info and policy specific for the pair of input parameters, like it's done in relate https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/relate/turns.hpp in the lines 46 and 41.  
  
Another alternative would be to modify the regular (A/A) policy and just set those position flags if specific turn_info type was used, or always for Linear geometries. The required parameters are passed into all turn policies, in regular policy they're just ignored because Areal geometries doesn't have the front or back (see https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp line 910).

---

## Comment 18

> Username: barendgehrels  
> Created_at: 2015-01-31 10:22:08 UTC  
> Updated_at: 2015-02-01 09:32:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#discussion_r23888085  

ratio = 0 should be checked. Checking equals(point,front) can make the result inconsistent.

---

## Comment 19

> Username: awulkiew  
> Created_at: 2015-01-31 12:31:12 UTC  
> Updated_at: 2015-02-01 09:32:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#discussion_r23888586  

In this case indeed the first and the last point should be EXACTLY the same so yes, maybe you're right that it'd be sufficient to check the ratios for exactly 0 or 1.

---

## Comment 20

> Username: mkaravel  
> Created_at: 2015-02-01 09:32:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/204#issuecomment-72357954  

Ratios are now used for testing the equality between turn points and endpoints.

---
