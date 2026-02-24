# #551 Feature/remove rescaling preparations [Merged]

> Username: barendgehrels  
> Created at: 2019-01-30 15:27:25 UTC  
> Updated at: 2019-02-10 09:56:27 UTC  
> Merged at: 2019-02-07 18:09:54 UTC  
> Closed at: 2019-02-07 18:09:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/551  

Please review this code. It prepares the removal of rescaling, by changes in code necessary in other parts than the intersection itself. These parts are: enrichment, traversal, get turn information.  
Also it adds some tests.  
  
There are no regressions.

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2019-02-06 10:51:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/551#pullrequestreview-200513198  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 141 |+         ctype const dx = get<0>(a) - get<0>(b);
 142 |+         ctype const dy = get<1>(a) - get<1>(b);
 143 |+         return dx * dx + dy * dy;
```

> Username: awulkiew  
> Created_at: 2019-02-06 10:51:55 UTC  
> Updated_at: 2019-02-07 18:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#discussion_r254221343  

Right, there should be similar tool as `equals_point_point` where you only include the algorithm for points. But this tool should take the strategy so it'd be ok to simply call comparable distance strategy here.  
  
This code is cartesian-only, is that correct?  
  
EDIT: This was simply moved, so I'm fine with not fixing it in this PR.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2019-02-06 10:53:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/551#pullrequestreview-200513658  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 654 |-         ctype const dy = get<1>(a) - get<1>(b);
 655 |-         return dx * dx + dy * dy;
 656 |-     }
```

> Username: awulkiew  
> Created_at: 2019-02-06 10:53:02 UTC  
> Updated_at: 2019-02-07 18:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#discussion_r254221780  

Right, it was moved from here. I'll update the comment above.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2019-02-06 10:56:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/551#pullrequestreview-200514978  

📁 include/boost/geometry/algorithms/detail/overlay/traversal.hpp

```diff
 380 |+         bool possible[2];
 381 |+         possible[0] = traverse_possible(next0);
 382 |+         possible[1] = traverse_possible(next1);
```

> Username: awulkiew  
> Created_at: 2019-02-06 10:56:12 UTC  
> Updated_at: 2019-02-07 18:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#discussion_r254222809  

Slightly inconsistent naming. Other variables have indexes at the end of the name instead of being an array. So `possible0`, `possible1`. Or is there a reason for this?

> Username: barendgehrels  
> Created_at: 2019-02-07 18:01:18 UTC  
> Updated_at: 2019-02-07 18:06:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#discussion_r254801819  

I answered earlier: "Good point. Not really, I will harmonize it." (but apparently I did something wrong, it was placed under the wrong comment).  
Anyway, there was a reason because it is used for iteration below.  
But I now harmonized it (using more arrays of 2 and iterations), will push.  
Thank you.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2019-02-06 10:58:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/551#pullrequestreview-200515788  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 214 |             // Q turns right on the left side of P (test "ML3")
 199 |-             // Union: take both operation
 215 |+                 // Union: take both operations
```

> Username: awulkiew  
> Created_at: 2019-02-06 10:58:12 UTC  
> Updated_at: 2019-02-07 18:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#discussion_r254223490  

tab?

> Username: barendgehrels  
> Created_at: 2019-02-06 17:10:58 UTC  
> Updated_at: 2019-02-07 18:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#discussion_r254364656  

I upgraded my VM and apparently the settings are not right yet, will fix. Thanks.

> Username: barendgehrels  
> Created_at: 2019-02-07 17:59:46 UTC  
> Updated_at: 2019-02-07 18:07:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#discussion_r254801187  

Was not a tab but the rest was missing an indent. Fixed now, pushed


---

## Comment 5

> Username: awulkiew  
> Created_at: 2019-02-06 11:04:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#issuecomment-460984231  

I'm ok with merging.

---

## Review 6 [Commented]

> Username: vissarion  
> Created_at: 2019-02-07 11:08:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/551#pullrequestreview-201028548  

Thanks! I am ok with merging.

📁 include/boost/geometry/algorithms/detail/overlay/traversal.hpp

```diff
 881 |+         }
 882 | 
 883 |+         // Traveral can either enter the cluster in the first turn,
```

> Username: vissarion  
> Created_at: 2019-02-07 11:05:39 UTC  
> Updated_at: 2019-02-07 18:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#discussion_r254636254  

Traversal...

> Username: barendgehrels  
> Created_at: 2019-02-07 18:01:46 UTC  
> Updated_at: 2019-02-07 18:06:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#discussion_r254802014  

Fixed, thnks


---

## Comment 7

> Username: barendgehrels  
> Created_at: 2019-02-07 18:09:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#issuecomment-461536187  

Thanks for your comments! They are addressed, will merge.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2019-02-09 11:22:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#issuecomment-462036020  

Something is wrong with this change. It causes assertion failure in all algorithms calling `get_turns`:  
  
    /boost/geometry/algorithms/detail/overlay/get_turns.hpp:149:  
    const Point& boost::geometry::detail::get_turns::unique_sub_range_from_section<IsAreal, Section, Point, CircularIterator, IntersectionStrategy, RobustPolicy>::at(std::size_t) const [with bool IsAreal = false; Section = boost::geometry::section<boost::geometry::model::box<boost::geometry::model::point<double, 2ul, boost::geometry::cs::cartesian> >, 2ul>; Point = boost::geometry::model::point<double, 2ul, boost::geometry::cs::cartesian>; CircularIterator = boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<const boost::geometry::model::point<double, 2ul, boost::geometry::cs::cartesian>*, std::vector<boost::geometry::model::point<double, 2ul, boost::geometry::cs::cartesian>, std::allocator<boost::geometry::model::point<double, 2ul, boost::geometry::cs::cartesian> > > > >; IntersectionStrategy = boost::geometry::strategy::intersection::cartesian_segments<>; RobustPolicy = boost::geometry::detail::no_rescale_policy; std::size_t = long unsigned int]:  
    Assertion `index < size()' failed.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2019-02-09 14:42:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#issuecomment-462049981  

Hmm, I get cannot see the regression matrix.  
However, I get a compilation error by the changes in 846d91cac

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2019-02-09 14:50:10 UTC  
> Updated_at: 2019-02-09 14:54:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#issuecomment-462050528  

After fixing that (is submitted), I don't get that assertion. You say "all algorithms", is it literally all?  
~~Can you say me which algorithm fails?~~  
Got it in intersection_linear_linear, looking at it right now.  
Sorry for the inconvenience

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2019-02-09 15:21:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#issuecomment-462052912  

Should be fixed, now running regression tests again.  
I now realize I did run them but in release mode, so didn't catch these assertions

---

## Comment 12

> Username: awulkiew  
> Created_at: 2019-02-10 00:30:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#issuecomment-462092485  

Looks good now, thanks: https://circleci.com/gh/boostorg/workflows/geometry  
  
> I don't get that assertion. You say "all algorithms", is it literally all?  
  
Well, I exaggerated ;) All set operations, is_simple and relate. See: https://circleci.com/workflow-run/c23a6228-8e9b-418b-a1d7-2845883f571c  
  
Thanks for fixing so fast!

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2019-02-10 09:56:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/551#issuecomment-462118738  

You are welcome ;-)

---
