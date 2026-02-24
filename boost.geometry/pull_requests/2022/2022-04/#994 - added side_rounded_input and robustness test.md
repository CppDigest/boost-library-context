# #994 [side] added side_rounded_input and robustness test [Merged]

> Username: barendgehrels  
> Created at: 2022-04-19 12:14:11 UTC  
> Updated at: 2022-06-22 11:14:41 UTC  
> Merged at: 2022-06-22 09:14:59 UTC  
> Closed at: 2022-06-22 09:14:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/994  

Add algorithm created by @tinko92 , including a unit test.  
  
The robustness test is not committed anymore in this PR (wip).  
  
The next PR (for slopes) will use this side algorithm.  
  
Old description:  
---------------------------------------------------------------------------------------------------  
  
  
Add robustness test for comparing intersection with sides, for the problem described in #993   
  
This test generates random segments and calculate the intersection points between them (if any).  
  
If there is one, it uses a side calculation to verify if the intersection point is determined as on-both-segments.  
  
Like this:  
![image](https://user-images.githubusercontent.com/334849/163999161-4f9bd22a-4f2b-4a29-8f7f-244066d03a37.png)  
  
  
It uses many side strategies we have (for cartesian) plus the new one of @tinko92   
  
The new one seems to be the best, by far:  
* it returns nearly always 0 (correct) for the intersection point being on the two lines  
* if an additional point is generated near the intersection point (bisecting_point), at a distance of several epsilons, it is determined as left or right (correct) of the two lines. That of course depends on the epsilon.  
  
So this side strategy seems the best to be used in the `buffer` `turn_in_ring_winding` algorithm.  
  
Herewith a sample output:  
  
```  
$ ./strategies/intersection_points_and_sides --count 1000000  --size 100 --multiplier 1.0e6 --type d  
  
Test configuration:  
  - Debug mode  
  - No rescaling  
  - Default test type: d  
  
Results  type: d  
  intersections: 231133  
  errors (triangle): 223186 0  
  errors (non robust): 224080 0  
  errors (side robust fp 3): 231133 0  
  errors (side robust eq 3): 230716 0  
  errors (rounded): 48 2  
  distance (avg): 2.6233714731400201749454688750541e-13  
  distance (avg, rnd): 1.4256091165024009451598202213168e-16  
  time: 3.558  
```  
  
As you can see, the errors using all the strategies we have are nearly as large as the samples. That means it is too precise (for this purpose) and always determines the intersection point either left or right of one (or both) of the two input lines. Which is inconvenient.  
For the extra point they determine it as correct (related to previous conclusion). But if that point goes closer, the results are still not good:  
```  
  intersections: 231171  
  errors (triangle): 223193 71469  
  errors (non robust): 224073 71801  
  errors (side robust fp 3): 231171 0  
  errors (side robust eq 3): 230788 14626  
  errors (rounded): 44 231171  
```  
So the strategy of @tinko92 is the only one where the intersection point is considered as ON both segments (nearly always) and if the point moves a bit away (about 1.0e5 epsilons) from it, usually considered as NOT on both segments.  
  
I think this will fix at least buffer, and it explains why my previous (short) attempt to just replace it was not yet successful (I don't think I reported that yet), it is currently too different from the other strategies.  
  
Also, it's less sensible to calculation type or field size.  
  
To be continued.

---

## Comment 1

> Username: tinko92  
> Created_at: 2022-05-02 00:28:04 UTC  
> Updated_at: 2022-05-04 05:55:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/994#issuecomment-1114380976  

Hi @barendgehrels   
  
I have some suggestions for the test. First, I would suggest allowing negative coordinates, e.g. by allowing a lower limit for the uniform_real_distribution. For uniform_real on [-1, 1], I get the following results:  
```  
  intersections: 231394  
  errors (triangle): 10239 0  
  errors (non robust): 213640 0  
  errors (side robust fp 3): 231394 0  
  errors (side robust eq 3): 15273 0  
  errors (rounded): 1718 0  
```  
  
I would also test points on a grid with rounded coordinates, e.g. by sampling from a uniform_int_distribution on [-1000, 1000] and dividing by 1000.0, because this might produce more pathological cases. This gives me the following results:  
  
```  
  intersections: 231557  
  errors (triangle): 11893 0  
  errors (non robust): 215074 0  
  errors (side robust fp 3): 231555 0  
  errors (side robust eq 3): 17545 0  
  errors (rounded): 1953 0  
```  
  
The errors for the rounded side strategy can be entirely eliminated (assuming no overflow/underflow) by doing the intersection calculation with increased precision (result computed with full precision, then rounded to target precision). You can find a commented implementation here: https://github.com/tinko92/geometry/blob/83b39dfe6592439aabc1853c5441e4373aade8ab/include/boost/geometry/util/precise_cartesian_intersection.hpp#L417  
  
Using that gives me:  
```  
  intersections: 231794  
  errors (triangle): 1241 0  
  errors (non robust): 194766 0  
  errors (side robust fp 3): 231794 0  
  errors (side robust eq 3): 0 0  
  errors (rounded): 0 0  
```  
This comes with a performance penalty. Here is a quick benchmark of computing intersections for 1,000,000 points with different algorithms and different coordinate distributions.  
(edit: benchmarks updated after optimizations and disabling turbo)  
|                           | uniform random [0, 100] | uniformly random [-1, 1] | grid [-1000, 1000] / 1000. |  
|---------------------------|-------------------------|--------------------------|----------------------------|  
| precise (rel. error <= eps)     | 2.81                    | 2.62                     | 2.98                       |  
| precise (rel. error <= 2eps)    | 1.78                    | 1.74                     | 1.88                       |  
| boost.geometry*                       | 0.16                    | 0.17                     | 0.17                       |  
| non-robust                 | 0.0058                  | 0.0058                   | 0.0058                     |  
| filtered (rel. error <= 99eps)** | 0.046                   | 0.11                     | 0.11                       |  
  
*For BG this test is not fair because I apply the intersection::cartesian_segments strategy and return intersections[0], so it does more than just the intersection calculation.  
**Filtered does the non-robust computation, checks whether a relative error of 99 eps can be guaranteed and computes some or all terms with more precision if that check fails. For an error tolerance of 99eps, a corresponding rounding side strategy (which would always find these points to be collinear) can be given, in this case, the coefficient for eps in the error bound would not be 5 but 404. This tradeoff of speed/precision is tunable.  
  
I understand that there is more to the intersection strategy than the line-line intersection formula. You can find all mentioned above and some further primitives for intersection calculations in https://github.com/tinko92/geometry/blob/add-side-rounded-input/include/boost/geometry/util/precise_cartesian_intersection.hpp .  
  
This is mostly debugged code but some of the used error bounds are definitely not optimal and there might be room for performance optimization in the algorithms.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2022-05-04 10:31:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/994#issuecomment-1117159071  

> I have some suggestions for the test. First, I would suggest allowing negative coordinates, e.g. by allowing a lower limit for the uniform_real_distribution. For uniform_real on [-1, 1], I get the following results:  
>   
  
Hi @tinko92 , thanks a lot for your comments, improvements and algorithms!  
  
I'm busy studying and testing them for my purpose and will come back to it.  
(I made a few minor comments in your commits in your fork)

---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2022-06-07 14:38:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/994#pullrequestreview-998286123  

📁 include/boost/geometry/strategies/cartesian/side_rounded_input.hpp

```diff
  35 |+                 double,
  36 |+                 CalculationType
  37 |+             >;
```

> Username: awulkiew  
> Created_at: 2022-06-07 14:38:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/994#discussion_r891319444  

Typically if `CT` is `void` we use the most precise coordinate type. Or is there a reason why `double` is used here?


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2022-06-07 14:45:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/994#pullrequestreview-998298391  

📁 include/boost/geometry/strategies/cartesian/side_rounded_input.hpp

```diff
  43 |+         CT _4 = get<1>(p2);
  44 |+         CT _5 = get<0>(p);
  45 |+         CT _6 = get<1>(p);
```

> Username: awulkiew  
> Created_at: 2022-06-07 14:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/994#discussion_r891327985  

There are two problems with these names. They are the same as Boost.Bind placeholders. Furthermore typically identifiers starting with `_` are reserved for predefined preprocesor macros. So I propose to change them.

> Username: awulkiew  
> Created_at: 2022-06-07 14:48:40 UTC  
> Updated_at: 2022-06-07 14:48:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/994#discussion_r891332359  

One more. They are confusing because these are numbers but they doesnt represents these specific numbers neither a position in some order etc. Better would be e.g. `x` or `p0`, etc.


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2022-06-07 14:49:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/994#pullrequestreview-998306708  

📁 include/boost/geometry/strategies/cartesian/side_rounded_input.hpp

```diff
  49 |+         const CT err_bound = (5 * eps + 32 * eps * eps) *
  50 |+             (  (std::abs(_1) + std::abs(_5)) * (std::abs(_4) + std::abs(_6))
  51 |+              + (std::abs(_3) + std::abs(_5)) * (std::abs(_2) + std::abs(_6)));
```

> Username: awulkiew  
> Created_at: 2022-06-07 14:49:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/994#discussion_r891333960  

Typically we use `math::abs`.


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2022-06-07 14:53:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/994#pullrequestreview-998313491  

📁 test/robustness/overlay/strategies/intersection_points_and_sides.cpp

```diff
 160 |+         const point_t p2 = random_point<point_t>(gen, dis);
 161 |+         const point_t q1 = random_point<point_t>(gen, dis);
 162 |+         const point_t q2 = random_point<point_t>(gen, dis);
```

> Username: awulkiew  
> Created_at: 2022-06-07 14:53:48 UTC  
> Updated_at: 2022-06-07 14:53:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/994#discussion_r891338775  

Random points probably shouldn't be usen in tests because various libraries may implement random generator differently so we may be potentially testing agsinst different points.


---

## Comment 7

> Username: awulkiew  
> Created_at: 2022-06-08 10:21:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/994#issuecomment-1149735430  

LGTM. Is this still a draft?

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2022-06-14 17:59:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/994#issuecomment-1155520258  

> LGTM. Is this still a draft?  
  
It was indeed, but next PR will be dependent on it.  
I'll probably remove the robustness test for now.

---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2022-06-14 21:10:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/994#pullrequestreview-1006587279  

📁 include/boost/geometry/strategies/cartesian/side_rounded_input.hpp

```diff
  44 |+                     >::type,
  45 |+                 CalculationType
  46 |+             >;
```

> Username: awulkiew  
> Created_at: 2022-06-14 21:10:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/994#discussion_r897326104  

All of these lines could be replaced with:  
```  
using coor_t = typename select_calculation_type_alt<CalculationType, P1, P2, P>::type;  
```  
from here:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/select_calculation_type.hpp#L54-L70


---

## Review 10 [Approved]

> Username: awulkiew  
> Created_at: 2022-06-14 21:12:19 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/994#pullrequestreview-1006588609  

LGTM

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2022-06-15 07:00:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/994#issuecomment-1156064564  

@vissarion OK?  
@tinko92 this is the new version, OK?

---

## Comment 12

> Username: tinko92  
> Created_at: 2022-06-22 09:42:30 UTC  
> Updated_at: 2022-06-22 11:14:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/994#issuecomment-1162882141  

> @tinko92 this is the new version, OK?  
  
Sorry, I missed the mention. I think it is ok!

---

## Comment 13

> Username: vissarion  
> Created_at: 2022-06-22 09:56:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/994#issuecomment-1162895998  

OK from me too and sorry for the delays.

---
