# #595 Fix inaccuracy in geographic point-segment distance computation [Merged]

> Username: vissarion  
> Created at: 2019-05-31 13:44:54 UTC  
> Updated at: 2019-06-25 09:59:58 UTC  
> Merged at: 2019-06-25 09:59:50 UTC  
> Closed at: 2019-06-25 09:59:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/595  

This PR proposes   
- a fix for an inaccuracy issue in point-segment distance computation for geographic coordinate systems,  
- some refactoring in the corresponding code,   
- an additional method for distance computation (bisection method) which is expected to be slower but more robust and it is added for testing purposes mostly  
- new tests  
  
The next example illustrates the issue.  
  
L = LINESTRING(-16.42203 -7.52882,4.89998 -6.15568)  
P1 = POINT(3.32696 -6.29345)  
P2 = POINT(3.3262, -6.28451)   
Note: P1, P2 lay in different sides of L  
  
1. BG (before fix)   
  
dist(L,P1)  
```  
spherical 508.0731159  
andoyer 2538.965064  
thomas 2539.230481  
vincenty 2539.22922  
```  
dist(L,P2)  
```  
spherical 489.553212  
andoyer 2544.15944  
thomas 2543.886244  
vincenty 2543.887587  
```  
2. BG (after fix)  
  
dist(L,P1)  
```  
spherical 508.0731159  
andoyer 480.3460262  
thomas 481.7457616  
vincenty 481.7390876  
```  
dist(L,P2)  
```  
spherical 489.553212  
andoyer 511.8654809  
thomas 510.4760589  
vincenty 510.4827314  
```  
3. BG (after fix / bisection method)  
  
dist(L,P1)  
```  
spherical 508.0731159  
andoyer 480.3460337  
thomas 481.7457826  
vincenty 481.7390876  
```  
dist(L,P2)  
```  
spherical 489.553212  
andoyer 511.8654777  
thomas 510.4760556  
vincenty 510.4827314  
```  
  
4. POSTGIS (version 2.5)  
  
dist(L,P1)  
```  
spherical 508.073115931  
geographic 505.320928239  
```  
dist(L,P2)  
```  
spherical 489.553211954  
geographic 486.901261892  
```  
  
Interestingly, POSTGIS uses a mixture of geographic + spherical computation to solve this problem but as illustrated above this could lead to results that are less accurate than simply using  the spherical computation.

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2019-06-16 21:50:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/595#pullrequestreview-250250315  

📁 include/boost/geometry/strategies/geographic/distance_cross_track.hpp

```diff
  84 |     typename Spheroid = srs::spheroid<double>,
  85 |     typename CalculationType = void,
  86 |+     bool Bisection = false,
```

> Username: awulkiew  
> Created_at: 2019-06-16 21:50:00 UTC  
> Updated_at: 2019-06-25 09:32:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/595#discussion_r294101727  

If it's only added for testing purposes should it be a part of the interface? You could e.g. implement a version of this strategy in details and outside expose the one without additional parameters.  
  
Is this also the case for the `EnableClosestPoint` parameter below?

> Username: vissarion  
> Created_at: 2019-06-18 13:06:35 UTC  
> Updated_at: 2019-06-25 09:32:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/595#discussion_r294810949  

>If it's only added for testing purposes should it be a part of the interface? You could e.g. implement a version of this strategy in details and outside expose the one without additional parameters.  
  
I exposed it in the interface since I think it could be potentially useful for some users (in some cases gives more accurate results; but taking more time). But if you insist I can hide it, I do not have a strong opinion since I do not research the variety of cases that bisection method is more accurate than the default.  
  
> Is this also the case for the EnableClosestPoint parameter below?  
  
This is going to be used by a new algorithm that returns the closest point, but since I didn't decide on the interface yet I can remove it for now.

> Username: awulkiew  
> Created_at: 2019-06-24 10:38:06 UTC  
> Updated_at: 2019-06-25 09:32:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/595#discussion_r296658995  

I think that if `Bisection` parameter is an important parameter which will be used often by the users it should probably be made the second one (like `SeriesOrder` in other strategies). Though it may be too late for that due to backward-compatibility issues so the reasonable approach is to leave it where it is now.  
  
However if it's not that important as you say and used only for testing then I think it should be removed and only exposed in some strategy defined in details which you could use in tests. If some user was interested it could be made public in any time.  
  
With that said this is not a strong opinion so I'm ok with one way or the other.

> Username: vissarion  
> Created_at: 2019-06-25 09:58:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/595#discussion_r297105489  

I hide the `Bisection` method into `strategy::distance::detail` namespace.


---
