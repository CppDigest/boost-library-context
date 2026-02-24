# #615 Stop passing policies to intersection, and pass subrange [Merged]

> Username: barendgehrels  
> Created at: 2019-08-21 15:55:39 UTC  
> Updated at: 2019-09-05 17:01:55 UTC  
> Merged at: 2019-09-05 17:01:49 UTC  
> Closed at: 2019-09-05 17:01:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/615  

This PR redefines code around segment_ratios such that it is not necessary anymore to pass them   
to most of the intersection strategies. This is a step forwards.  
  
Also, it passes the subrange to intersection i/o the segment. This can (incidentally) be used to avoid start turns. It is not used yet now but will be in a next PR.  
  
_More in detail, in turn_info_helpers the split is made such that only if robust policy is really used, it is passed to intersection. Cartesian only (automatically, by nature of policy, the rest does not use rescaling). Therefore RobustPolicy is not present anymore in spherical/geographic. Later (but all is transitional to avoid mega-PR's) it will also be omitted from Cartesian._

---

## Comment 1

> Username: awulkiew  
> Created_at: 2019-08-25 20:58:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/615#issuecomment-524663122  

Thanks! Though I'm confused by the change from segments to subranges in intersection strategies. Is it because you plan to pass more than 2 points into intersection strategies later? If that is the case how will the intersection strategy work for 3 points and what would these 3 points be? If it is not the case why did segments replaced?

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2019-08-26 14:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/615#issuecomment-524878865  

Sure, I plan to use more than 2 points later some in rare cases. This makes the start turns, which were there earlier and quite complex, redundant (so that code, which was not used at this moment, is now  deleted in this PR).  
The 3 points are effectively passed already right now, because the subrange is passed. But the third point is not used yet. The third point can make the difference in collinear cases between a collinear situation and a touch (both arcs meet at their endpoints). That difference is otherwise, in Floating Point coordinates, in rare cases, very hard to make without that third point

---

## Comment 3

> Username: awulkiew  
> Created_at: 2019-08-26 16:56:54 UTC  
> Updated_at: 2019-08-26 16:59:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/615#issuecomment-524939306  

> The third point can make the difference in collinear cases between a collinear situation and a touch (both arcs meet at their endpoints). That difference is otherwise, in Floating Point coordinates, in rare cases, very hard to make without that third point  
  
I don't know the algorithm you're planning to implement but shouldn't this be rather a part of the algorithm and not the strategy? How the information about the third point (next in range?) will change the calculation of the intersection point of two segments? Or in other words, is it possible to check some specific conditions before calling the strategy or afterwards in the algorithm? Or is the check CS-dependent?  
  
If this is CS-dependent then my gut feeling is that maybe all of the calculation so intersection+sideness of the next segment(s) should be done once in the intersection strategy for all cases, not only this one? Then you'd move significant part of get_turn_info into the intersection strategies so that'd make it even more complex. (EDIT: but in this case get_turn_info would become simpler or obsolete and this would be good). But you propose to handle only part of the analysis of next segment in the intersection strategy so this is confusing.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2019-09-04 13:12:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/615#issuecomment-527894257  

The whole idea is to create a "hook"like intersection. So two segments are intersected with two others, and if the intersection is located on both first segments, it is reported. The second segments are only involved as soon as the intersection falls exactly on the midpoint. That way we can guarantee intersection behaviour, and don't need start-turns. We won't miss intersections then (which now might happen, though in really rare cases).  
  
Having said that, that can be build around it and not inside indeed. But the problem is that intersection strategy also calls all policies (how, arrival, sides, etc) and those are dependent on that point. Therefore I think it is better to have them inside. And maybe, indeed, it is wise to create a simplier intersection strategy and have this one CS agnostic (it is already very large anyway - but those other strategies assembled there do belong per CS...)

---

## Comment 5

> Username: awulkiew  
> Created_at: 2019-09-04 13:22:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/615#issuecomment-527898280  

I was rather thinking about detecting it before calling the strategy. Is it even possible or is it only possible after the intersection point is calculated, so internally in the strategy or after?  
  
> and have this one CS agnostic  
  
This is only a side note. If something is really CS agnostic (so has to be calculated/called regardless of the strategy) it should rather be moved outside to the algorithm.  
  
I'm not saying to do it here because my gut feeling is that the handling of special cases related to numeric issues is not CS agnostic.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2019-09-04 14:42:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/615#issuecomment-527932662  

No, it is not possible to detect it before. It depends on all the information calculated during segment intersection.  
  
> I'm not saying to do it here because my gut feeling is that the handling of special cases related to numeric issues is not CS agnostic  
  
That makes sense, often it is not.  
  
So do you think this PR is acceptable?

---

## Comment 7

> Username: awulkiew  
> Created_at: 2019-09-04 16:00:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/615#issuecomment-527967894  

Yes, I'm ok with it. Thanks!

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2019-09-04 16:02:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/615#issuecomment-527969101  

Thank you! Will merge it soon, @vissarion are you also OK?

---

## Review 9 [Approved]

> Username: vissarion  
> Created_at: 2019-09-05 08:00:22 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/615#pullrequestreview-284079016  

Thanks, I am OK too.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2019-09-05 17:01:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/615#issuecomment-528464810  

Thanks!

---
