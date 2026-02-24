# #421 Formula for meridian distance in geography [Merged]

> Username: vissarion  
> Created at: 2017-09-07 10:05:42 UTC  
> Updated at: 2018-05-08 08:47:25 UTC  
> Merged at: 2017-10-31 14:51:16 UTC  
> Closed at: 2017-10-31 14:51:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/421  

This PR implements and tests formulas for computing distance on a meridian of the ellipsoid, i.e. the length of an elliptic arc. This is a special case and should be faster than general distance computation. Apart from the special case it is useful for distance point-box and box-box.   
  
Methods tested:   
- altered andoyer formula   
- Iterative method to elliptic arc length from  http://www.codeguru.com/cpp/cpp/algorithms/article.php/c5115/Geographic-Distance-and-Azimuth-Calculations.htm  
- implementation of the series expansion to the elliptic integral, cf. https://en.wikipedia.org/wiki/Meridian_arc  
  
Some experimental results:  
  
haversine formula (sph), andoyer (and), altered andoyer (andm), thomas (thom), vincenty (vinc), iterative method (iter), series expansion of order $k (eli$k)  
  
lat1=0, lat2=.1  
```  
method   time(sec)              distance  
--------------------------------------------------  
sph  0.21896799999999999597 11122.625530463203177  
and  0.39223000000000002307 11057.302557857787178  
andm 0.32905499999999998639 11057.302557835722837  
thom 0.80068499999999998007 11057.42769490495084  
vinc 0.44403500000000001302 11057.427694918429552  
iter 0.24568200000000001149 11057.426291254290845  
eli0 0.21529799999999998938 11113.28742053105816  
eli1 0.21665499999999998648 11057.302557835424523  
eli2 0.23433799999999999075 11057.427905032423951  
eli3 0.24891499999999999737 11057.427694549418447  
eli4 0.26759100000000002328 11057.427694901480209  
eli5 0.28361300000000000399 11057.427694900879942  
```  
lat1=0, lat2=1  
```  
method   time(sec)              distance  
--------------------------------------------------  
sph  0.2200620000000000076  111226.25530463203904  
and  0.39458500000000001906 110573.1381278111221  
andm 0.34521800000000002484 110573.13812782216701  
thom 0.79639400000000004631 110574.38855758057616  
vinc 0.43557600000000001872 110574.38855796010466  
iter 0.23557100000000000262 110573.85710695103626  
eli0 0.22451399999999999135 111132.8742053105816  
eli1 0.22065000000000001279 110573.13812782228342  
eli2 0.23338400000000000811 110574.39065498989657  
eli3 0.2682990000000000097  110574.38855428357783  
eli4 0.26395999999999997243 110574.38855779086589  
eli5 0.28200300000000000367 110574.38855778488505  
```  
lat1=0, lat2=82  
```  
method   time(sec)              distance  
--------------------------------------------------  
sph  0.23926200000000000245 9120552.9349798262119  
and  0.39393699999999998163 9108474.8711581416428  
andm 0.34638000000000002121 9108474.8711581416428  
thom 0.82127600000000000602 9108472.3486985526979  
vinc 0.44946399999999997465 9108472.3605172522366  
iter 3.7206640000000001933  9107483.6757286004722  
eli0 0.21355699999999999683 9112895.6848354674876  
eli1 0.22923199999999999132 9108474.8711581416428  
eli2 0.25518600000000002392 9108472.3753185868263  
eli3 0.28539599999999998303 9108472.3605391923338  
eli4 0.30729699999999998683 9108472.3605174832046  
eli5 0.33749899999999999345 9108472.3605174515396  
```  
lat1=70, lat2=80  
```  
method   time(sec)              distance  
--------------------------------------------------  
sph  0.23153799999999999382 1112262.553046320565  
and  0.42211700000000001998 1116152.6053681580815  
andm 0.34255900000000000238 1116152.6053681566846  
thom 1.0010479999999999379 1116159.1440612713341  
vinc 0.47071600000000002328 1116159.144166109385  
iter 0.64702700000000001879 1116147.4295761580579  
eli0 0.21242300000000000071 1111328.7420531054959  
eli1 0.23471500000000000696 1116152.6053681569174  
eli2 0.28513899999999997581 1116159.1458909632638  
eli3 0.32849899999999998546 1116159.1441906830296  
eli4 0.37058099999999999374 1116159.1441666036844  
eli5 0.41437499999999999334 1116159.1441665440798  
```  
Concluding remarks:  
The iterative method is efficient for small segments and becomes very slow as the segment grows. The series expansion is faster than altered andoyer. For order 1 the new formula is a bit less than 2x faster than andoyer for computing meridian distance while keeps the same accuracy.    
  
Proposal:  
Use the new series expansion for meridian distance of order 1,2,3 for formulas of andoyer, thomas, vincenty respectively.   
  
---EDIT---  
All above times are produced in debug mode, the following table is release:  
```  
method               distance          time(sec)   
--------------------------------------------------  
and  	 31823262.5346244 	1.244068  
thom  	 31822955.2147363 	3.236715  
vinc  	 31822955.2958325 	1.328228  
eli0   	 31837223.285613 	2e-06  
eli1   	 31823262.534624        0.003459  
eli2   	 31822955.120772 	0.003468  
eli3   	 31822955.2944491 	0.003485  
eli4   	 31822955.2958287 	0.003461  
eli5   	 31822955.2958276 	0.003489  
```

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2017-09-07 23:44:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/421#pullrequestreview-61382103  

📁 include/boost/geometry/formulas/andoyer_inverse.hpp

```diff
 154 |+             else if (math::equals(lon1, lon2))
 155 |+             {
 156 |+                 result.azimuth = c0;
```

> Username: awulkiew  
> Created_at: 2017-09-07 23:44:20 UTC  
> Updated_at: 2017-10-31 14:50:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/421#discussion_r137681805  

The azimuth can also be `pi` in this case if `lat2 < lat1`. Maybe also -pi if `lon2 < lon1`, so very close to each other, idk.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2017-09-08 00:57:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/421#pullrequestreview-61390757  

📁 include/boost/geometry/formulas/andoyer_inverse.hpp

```diff
  83 |+         CT sin_dlon;
  84 |+         CT cos_lat1;
  85 |+         CT cos_lat2;
```

> Username: awulkiew  
> Created_at: 2017-09-08 00:57:02 UTC  
> Updated_at: 2017-10-31 14:50:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/421#discussion_r137689670  

These variables are not initialized which is wrong by itself but even worse later thay're used for azimuth and quantities calculation. Something meaningful is assigned to them only if `! math::equals(lon1, lon2)` so uninitialized variables may be used below. Or am I missing something?  
  
In general I think it is desireable to define and initialize variabels at once, if that'd require to redesign the algorithm/formula then maybe this would be the way to go?


---

## Comment 3

> Username: awulkiew  
> Created_at: 2017-09-08 01:04:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/421#issuecomment-327971020  

Regarding the modified andoyer formula. Is this modification formula-specific so other formulas should be modified in a different way or could it be applied also to thomas and vincenty so extracted outside the formula?  
  
Aside from the above. What do you think about first checking if `equals(lon1, lon2)` before all of the computation and only after that call special version of algorithm or the general one? Or would both versions share great ammounts of code?  
  
Shouldn't the optimization also kick in when `lon2-lon1 == pi` ?

---

## Comment 4

> Username: vissarion  
> Created_at: 2017-09-08 08:38:42 UTC  
> Updated_at: 2017-09-08 08:41:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/421#issuecomment-328040817  

The code of modified andoyer formula is prototype and mostly for testing. As stated above I propose to apply the new formula of series expansion for all distance formulas (andoyer, thomas, vincenty) with the corresponding order (i.e. 1,2,3 resp.). In this way, both modified andoyer and iteration method are redundant w.r.t. experiments above, so they can be dropped. What do you think?  
  
One way for doing this is inside the distance strategy, i.e. if  `equals(lon1, lon2) || lon2-lon1 == pi` call the new series expansion formula of arc length of an ellipse otherwise call the current formula. This way I think we overcome the above issues on uninitialized variables. Similarly (as @awulkiew said above) the distance formula could check before computations if `equals(lon1, lon2) || lon2-lon1 == pi` and call the new formula but now functions do not share code which is a plus.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2017-09-13 13:27:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/421#issuecomment-329167465  

Yes, the results of new formula of series expansion looks good and the performance is improved. It seems that the resutls of Vincenty's are more close to series expansion order 4, so maybe this is the correct order? It's hard to compare both since Vincenty's is iterative. Have you checked Karney's?  
  
Yes, I agree that both formulas shouldn't be mixed and that the strategy is a good place to check the longitudes and call either of these formulas. Also I think that we shouldn't add additional template parameter defining order of the series but simply use the default one internally. Though interestingly enough (assuming you agreed about using order 4 for vincenty) the orders would be the same as the ones used with other series in intersection and area strategies (https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/geographic/parameters.hpp#L100). However here it's only and optimization, not the core.  
  
Side note, the "full" check for the meridian arc could look like this (`units_t` is `radian` or `degree`):  
  
    calc_t c0 = 0;  
    calc_t pi = math::detail::constants_on_spheroid<calc_t, units_t>::half_period();  
    calc_t diff = math::longitude_distance_signed<units_t>(lon1, lon2);  
    if (equals(diff, c0) || equals(math::abs(diff), pi))  
    { /*...*/ }

---

## Comment 6

> Username: vissarion  
> Created_at: 2017-09-28 16:13:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/421#issuecomment-332886768  

As far as I understand, Karney's (i.e. geographiclib) distance computation is Vincenty plus a method for nearly antipodal points.   
  
I activated the meridian distance computation on distance strategy as a special case. For the mapping between formulas (andoyer, thomas, vincenty) and orders of series I used the existing ones i.e. andoyer --> 1, thomas --> 2, vincenty --> 4.   
  
It seems that the results of meridian distance are very similar to the general formulas' results. There is one exception though: nearly antipodal points. In that case there is a (large) difference in results since the new formula produce accurate results for that case while formulas like andoyer results with low accuracy. That is, given two nearly antipodal points the distance strategy for andoyer will return a different result than the andoyer's formula. Cf. test cases that are commented out; these are failing. I see two simple directions here: either leave the code as is and change the unit tests in order not to fail, or keep the old distance strategy without using the meridian formula and use the new formula explicitly whenever needed e.g. in pt-box distance. Of course, we can discuss more involved solutions but I think this will eventually end up in implementing accurate solutions for nearly antipodal points, which is not the focus of this PL.  
  
Apart from the inconsistency in the result from strategy vs. formula, there is also an issue when computing distances using for example the distance function. Consider the case of two nearly antipodal points on the meridian and compare it with the case where one of two points is moved slightly away from the meridian. In the later case the original formula will be used by the distance function and there will be a "jump" in the distance result. However, even in the current code the user would experience a "jump" when computing distances for nearly antipodal points, cf. https://github.com/boostorg/geometry/issues/426

---

## Comment 7

> Username: awulkiew  
> Created_at: 2017-10-31 14:44:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/421#issuecomment-340784695  

Thanks, I'm ok with altering the test results for now and in the long run implement proper solution for antipodal areas.

---

## Comment 8

> Username: adl1995  
> Created_at: 2018-05-07 14:24:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/421#issuecomment-387081442  

Following up on handling the case of nearly antipodal points which lie outside the meridian, I think a similar solution could be devised i.e. check if the points are antipodal in the distance strategy (`strategies/geographic/distance.hpp`) and call the modified function accordingly.  
  
Also, as far as I understand, Karney's solution seems to be using a modified algorithm, which is not identical to Vincenty. It also performs some preprocessing, such as arranging the latitudes, longitudes in a canonical form, and treats points as being on the meridian or the equator if they are close to it (these points are then treated as a special case).  
  
@vissarion Can you please confirm if the solution above works well for nearly antipodal points that lie on the meridian? If so, I should focus on the general case of Karney's solution i.e. when the geodesic is neither meridional or equatorial.  
  
As a starting point, I'm thinking of adding a check for antipodal points which calls the modified algorithm (a dummy function for now). From what I understand, Karney's solution makes use of the longitudinal difference for determining if the points are antipodes.  
  
@vissarion, @awulkiew - If you have further suggestions regarding how the solution should be structured, please let me know.

---

## Comment 9

> Username: vissarion  
> Created_at: 2018-05-08 08:47:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/421#issuecomment-387331089  

@adl1995 This formula works well for meridian nearly antipodal points. However, your solution should be consistent for both cases of meridian and non-meridian antipodal points. That is, one should not experience changes in accuracy when moving from a point on meridian to a nearby not on meridian.

---
