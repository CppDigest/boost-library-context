# #201 Feature: New geographic distance and side strategies [Merged]

> Username: awulkiew  
> Created at: 2015-01-25 19:21:50 UTC  
> Updated at: 2017-03-20 14:27:51 UTC  
> Merged at: 2015-06-03 07:12:07 UTC  
> Closed at: 2015-06-03 07:12:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/201  

This PR adds two formulas for inverse solution of geodesics on the ellipsoid which besides vincenty_inverse could be used to calculate the geographic distance and azimuth. Both are Forsyth-Andoyer-Lambert type approximations:  
- andoyer_inverse - first order,  
- thomas_inverse - second order.  
  
I named this 2nd-order-approximation thomas_inverse because it was described in the technical report by Paul D. Thomas and in the web I saw it was refered as Andoyer-Lambert-Thomas. But feel free to propose different naming. It's being said that while the 1st order approximation allows to calculate the distance with ~20meters precision, the 2nd order allows to do this with 1meter precision.  
  
The andoyer_inverse gives the same results as the "old" andoyer distance implementation but it uses smaller number of math functions (trigonometric + sqrt) and smaller number of multiplications.  
  
This PR also adds a new thomas distance strategy.  
  
I also plan to implement 3 side strategies based on Andoyer, Thomas and Vincenty formulas.  
  
Example results (a=1, b=0.75, haversine_r = (2*1+0.75)/3, azimuths in radians):  
  
```  
p1:         (-60, -60)  
p2:         (60, 60)  
DISTANCES  
vincenty:     2.2661986  
andoyer orig: 2.2273721  
haversine:    2.4165461  
andoyer:      2.2273721  
thomas:       2.2532231  
AZIMUTHS  
vincenty:     1.3059514  
andoyer:      1.2682832  
thomas:       1.2905275  
```

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-01-25 19:46:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/201#issuecomment-71388732  

Thanks.  
  
This is more or less a new subject, different from most of the things we did for 1.58  
Do you mind if we postpone this until after the 1.58 release?

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-01-25 19:46:32 UTC  
> Updated_at: 2015-05-29 14:30:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/201#discussion_r23506898  

Curly braces are missing

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-01-25 19:47:27 UTC  
> Updated_at: 2015-05-29 14:30:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/201#discussion_r23506913  

member variables should be called m_a, m_b, etc

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-01-25 19:47:52 UTC  
> Updated_at: 2015-05-29 14:30:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/201#discussion_r23506917  

Please add space between ! and math (above too)

---

## Comment 5

> Username: awulkiew  
> Created_at: 2015-01-25 21:32:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/201#issuecomment-71394723  

@barendgehrels Of course we can postpone it. Though it's not very different than what was done for 1.58 since vincenty and andoyer distance strategies was moved from extensions. However this was not documented yet.  
  
I also have done some tests. On a picture below there are curves generated using all of the methods (https://github.com/awulkiew/test-geoside). The algorithm generating them (the geographic ones) was more or less like as if someone was navigating using the inverse formulas. So iteratively an azimuth to the target point is calculated using XXX_inverse formula and then the next point is calculated using the vincenty_direct (using some small distance step). I'm not sure if this is accurate but you can get the picture about the differences.  
  
We could think about replacing the default SSF with e.g. Andoyer which is used by default to calculate the distance, so the default side and distance would be compatible.  
  
We could also think about the comparable distance and maybe more interesting comparable azimuth. I haven't seen it anywhere but it could be useful for side calculation.  
  
Below:  
- white - vincenty  
- cyan - thomas  
- magenta - andoyer  
- red - spherical  
  
![geodesics](https://cloud.githubusercontent.com/assets/1226951/5893305/c2b31e84-a4de-11e4-8936-618dee2a35e7.png)  
  
The lengths of the curves calculated in 3d are:  
  
```  
vincenty:       2.2205473  
thomas:         2.2208485  
andoyer:        2.22212  
spherical:      2.2704009  
```

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2015-01-26 07:54:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/201#issuecomment-71424369  

@awulkiew true - but it is still a bit different because andoyer/vincenty were waiting in extensions already 5 years or longer, and this is brand new. We planned to harmonize earth model with projection classes, and we partly did but it is not yet finished. So in hindsight we maybe should not yet have move them. OK, no problem. But let's postpone further steps for next release. You agreed, so OK.  
  
It looks very good, the results of the Thomas method look promising!

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-05-11 21:54:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/201#issuecomment-101059781  

I have not looked at the code, but I have a couple of generic comments/questions:  
1. What are the advantages over existing strategies we have?  
2. Where do we plan to use them?  
3. Would it make sense to have them original as extensions and in due time (possibly after the work on projections by Barend is done), add them to the library?

---

## Comment 8

> Username: awulkiew  
> Created_at: 2015-05-12 13:27:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/201#issuecomment-101276473  

1. Currently for geographic CS by default andoyer strategy is used for distance and **spherical** side formula for side (used in point_in_geometry, within, etc.). And there is also vincenty distance strategy which can be used for distance with great accuracy. This PR adds andoyer side strategy and uses it by default for side calculation for geographic CS. It also adds vincenty side strategy for consistency in case if someone wanted to check e.g. `within` with better precision. And it adds andoyer-type approximation distance and side strategies which is more precise than andoyer and faster than vincenty in case a user desired it.  
2. I think 1 answers that  
3. Sure, if that's desireable. We could also have them in the main code but not documented, just like geographic distance strategies right now. I haven't documented them on purpose after moving from extensions.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-06-02 07:56:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/201#issuecomment-107847175  

I am okay with merging this PR. We can experiment with it and then make the functionality public a bit later on.  
Barend?

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2015-06-02 19:23:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/201#issuecomment-108065478  

Sure, I'm OK with merging too.

---
