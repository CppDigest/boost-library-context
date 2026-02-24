# #500 Introduce formula for Karney's inverse geodesic method [Merged]

> Username: adl1995  
> Created at: 2018-07-10 06:57:12 UTC  
> Updated at: 2019-10-31 15:00:32 UTC  
> Merged at: 2019-10-31 15:00:31 UTC  
> Closed at: 2019-10-31 15:00:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/500  

The implementation for Karney's inverse method is almost complete. The test cases are similar to Karney's direct method i.e. taken from GeographicLib. However, there a some bugs in the calculation which leads to failure in test cases.  
  
Once those are resolved, you could review the code for any improvements.

---

## Comment 1

> Username: adl1995  
> Created_at: 2018-07-13 07:20:24 UTC  
> Updated_at: 2018-07-13 14:15:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#issuecomment-404748832  

@vissarion, @awulkiew - I have resolved the bug which was causing test failures.  
  
Since all tests are passing now, please feel free to review the code.

---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-13 12:27:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/500#pullrequestreview-145629167  

📁 include/boost/geometry/util/math.hpp

```diff
 827 |+       std::numeric_limits<T>::quiet_NaN() :
 828 |+       std::numeric_limits<T>::max();
 829 |+ #endif
```

> Username: awulkiew  
> Created_at: 2018-08-13 12:27:34 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r209589470  

Parentheses around `max` would work in all compilers `#if` is not needed.  
  
But there is a greater problem with this code. It will not work for user-defined, non-fundamental types because for them `numeric_limits` will not be specialized. So I'd remove `NaN` function and replace it with some other mechanism.

> Username: adl1995  
> Created_at: 2019-03-17 11:09:45 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r266234567  

`math::NaN()` was previously being used when the angle was greater  
than 90 deg. Since we now use the maximum angle value [-90, 90]  
(see comment for "NaN() for fundamental types may return `quiet_nan`  
 or `max` depending on the compiler.") this function is no longer required.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-13 12:31:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/500#pullrequestreview-145630412  

📁 include/boost/geometry/util/math.hpp

```diff
 835 |+ template<typename T>
 836 |+ inline T round_angle(T x) {
 837 |+     static const T z = 1/T(16);
```

> Username: awulkiew  
> Created_at: 2018-08-13 12:31:24 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r209590461  

What does this function do exactly? The name is not specific enough  
  
Why exactly 1/16 is used here? If this was taken from GeographicLib my guess would be that this exact number is choosen for series of `double` numbers. But what if a user pass more precise numeric type?

> Username: cffk  
> Created_at: 2019-01-28 14:41:46 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r251441713  

This routine reduces accuracy of very small floating point numbers.  
This was motivated by the unneeded accuracy that floating point numbers  
offer for measurements of azimuth (or latitude or longitude) where  
azimuths near 0 deg are represented with much more accuracy than those  
near 180 deg.  It was introduced by me into GeographicLib so that I need  
not worry the behavior of a geodesic defined by, for example,  
  
    lat, lon, azi = 45, 0, 1e-300 deg  
  
This routine means that the smallest postive angle is 0.5^57.  Its  
introduction was partly laziness on my part.  
  
It would be fine to remove this function (replacing it by the identity)  
provided that additional tests were introduced involving tiny angles  
(latitude, longitude, and azimuth).  
  
The number 1/16 doesn't have to be adjusted for precision.  The function  
results in angles near 0 having a resolution about 1000 finer than near  
90 deg, independent of precision.

> Username: adl1995  
> Created_at: 2019-03-17 11:09:49 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r266234571  

@cffk - Thanks for the explanation.  
  
When you say to replace this function with identity, does it mean to simply use the original value without modification?  
  
As for the tests, we currently have some test cases for small angles (https://github.com/boostorg/geometry/blob/develop/test/formulas/inverse_cases.hpp#L44), would these be sufficient?

> Username: cffk  
> Created_at: 2019-03-17 12:46:57 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r266237889  

Yes, by identity I meant return the value without modification.  (But *maybe* it should convert -0 to +0?)  
  
The tests should include the cases I alluded to above:  
  
    lat, lon, azi = 45, 0, eps  
  
where `eps = +/- ( 0.0, 1e-300, 1e-200, 1e-100, 1e-50, 1e-20, 1e-10)`.  I didn't see such tests where you pointed me to, maybe because you're only dealing with the inverse problem.  For than you want, e.g.,  
  
    lat1, lon1, lat2, lon2 = 0, 180, 0, eps  
    lat1, lon1, late, lon2 = eps1, 0, eps2, 170  
  
where `eps, eps1, eps2` are similarly small quantities.  You may also want to mix up these two cases.

> Username: adl1995  
> Created_at: 2019-03-31 09:25:34 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r270657571  

@cffk - Thanks for the input. I have removed the `round_angle()` function and all tests seem to run fine. I'm not sure if would be any corner cases that could benefit from the conversion of -0 to +0.  
  
I have added the following test case for inverse calculation:  
Input:  
```  
lon1=180, lat1=0, lon2=1e-300, lat2=0  
```  
  
The following table shows a comparison of the values I get:  
  
| Results (inverse) 	| Karney (GeoLib)            	| Karney (Boost)             	| Vincenty (Boost)       	| Thomas 	| Andoyer                	|  
|-------------------	|----------------------------	|----------------------------	|------------------------	|--------	|------------------------	|  
| s12               	| 20003931.45862544700503349 	| 20003931.45862544700503349 	| 19970326.371122572571  	| 0      	| 20037508.342789243907  	|  
| azi1              	| -0                         	| -0                         	| -90                    	| 0      	| -0                     	|  
| azi2              	| 180                        	| 180                        	| -90                    	| 0      	| 180                    	|  
| m12               	| 67125.61229850351810455322 	| 67125.61229850351810455322 	| -67180.721021872202982 	| 0      	| -67180.721021872202982 	|  
| S12               	| -1                         	| -1                         	| 1                      	| 1      	| -1                     	|  
  
I think this is the expected behavior as all methods other than Karney are not accurate.  
  
@awulkiew, @vissarion - For the test cases that do not produce correct results, should I simply ignore them, or add the expected (incorrect) values in the tests?  
  
(I haven't yet added tests for `lat1, lon1, late, lon2 = eps1, 0, eps2, 170`)

> Username: vissarion  
> Created_at: 2019-04-01 07:47:08 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r270747144  

>@awulkiew, @vissarion - For the test cases that do not produce correct results, should I simply ignore them, or add the expected (incorrect) values in the tests?  
  
If you mean the results of `vincenty`, `thomas` and `andoyer` for nearly antipodal point, I think we should ignore the results.

> Username: adl1995  
> Created_at: 2019-04-06 12:31:26 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r272794328  

I have added the further test cases for Karney's inverse method involving small angles.  
The values below follow this format:  
```  
{ lon1, lat1 },{ lon2, lat2 },  
```  
```  
{ 0, 1e-100 },{ 170, 1e-200},  
{ 0, 1e-300 },{ 170, 1e-50},  
{ 0, 1e-300 },{ 170, 1e-10},  
{ 0, 1e-100 },{ 170, 1e-50},  
{ 0, 1e-200 },{ 170, 1e-50},  
{ 0, 0.0 },{ 170, 1e-10},  
{ 0, 1e-20 },{ 170, 1e-100},  
{ 0, 1e-100 },{ 170, 0.0},  
{ 0, 1e-10 },{ 170, 1e-300},  
{ 0, 1e-300 },{ 170, 1e-100},  
{ 0, 1e-200 },{ 170, 1e-100},  
{ 0, 1e-10 },{ 170, 1e-50},  
{ 0, 0.0 },{ 170, 1e-200},  
{ 0, 0.0 },{ 170, 1e-10},  
```  
  
There are currently 5 failing test cases (the results obtained from Boost are compared with GeographicLib):  
  
```  
====== BEGIN OUTPUT ======  
Running 1 test case...  
test/formulas/test_formula.hpp(61): error: in "test_main_caller( argc, argv )": karney_gs (0 1e-300)->(170 1e-10) : result {-0.98649192825634202109} different than expected {852.38563572856332939}.. 865.057  
test/formulas/test_formula.hpp(81): error: in "test_main_caller( argc, argv )": karney_gs (0 1e-300)->(170 1e-10) : result {-0.98649192825634202109} and reference {852.38563572856332939} not close enough.  
test/formulas/test_formula.hpp(61): error: in "test_main_caller( argc, argv )": karney_gs (0 0)->(170 1e-10) : result {-0.98649192825634202109} different than expected {852.38563572856332939}.. 865.057  
test/formulas/test_formula.hpp(81): error: in "test_main_caller( argc, argv )": karney_gs (0 0)->(170 1e-10) : result {-0.98649192825634202109} and reference {852.38563572856332939} not close enough.  
formulas_inverse: ../../boost/geometry/util/normalize_spheroidal_coordinates.hpp:413: void boost::geometry::math::normalize_unit_vector(ValueType&, ValueType&) [with ValueType = double]: Assertion `h > 0' failed.  
```  
  
The tests only fail for geodesic scale, as the value reported by GeographicLib is around `852`, whereas the value obtained using the Boost implementation is around `-0.98`.  
  
@cffk Can you please comment on this. Since all other values are around `-0.98`, there might be a bug in GeographicLib which causes an anomaly in the results.

> Username: cffk  
> Created_at: 2019-04-06 14:10:37 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r272796940  

I think you've copied the results from GeographicLib incorrectly.  `852` is the value of the area `S12` not the geodesic scale.  This is what I get with the MPFR version of GeographicLib:  
  
    echo 0 0 0.0000000001 170 | tools/GeodSolve -i -f -p 20  
    0.0 0.00 89.9999999993915829658826580  
    0.0000000001 170.0 90.0000000006001984931704213  
    18924313.43485650735011121714 170.5718952695659912130518074  
    1041298.80855225030444757764  
    -0.986491928256342019757132341 -0.986491928256342019757132146  
    852.3856243479945  
  
So the geodesic scales `M12` and `M21` are both about `0.98`.

> Username: adl1995  
> Created_at: 2019-04-06 19:20:13 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r272806633  

Apologies, it was my bad. I was creating a dummy variable `_` and tried to discard `M21` and `S12` with:  
```  
geod.Inverse(lat1, lon1, lat2, lon2, distance, azimuth, reverse_azimuth, reduced_length, geodesic_scale, _, _);  
```  
Since the values were passed by reference, I think this was what caused the anomaly.  
  
I have updated the test cases. The following test case fails:  
```  
{ 0, 0.0 },{ 170, 1e-10 },  
```  
I think I found a corner when both `lat1` and `lon1` are `0`. In this case the `math::normalize_unit_vector` function is called with:  
```  
math::normalize_unit_vector<CT>(sin_beta1, cos_beta1);  
```  
where `cos_beta1` is less than `0`, so the function raises an exception. @cffk How does GeographicLib handle this case? I could not find a precondition in the code base which avoids division by `0`.

> Username: cffk  
> Created_at: 2019-04-06 22:12:54 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r272811025  

I don't see how there can be a problem normalizing `sin_beta1` and `cos_beta1`.   When the latitude is close to 0, `cos_beta1` is close to `1`.  As long as you've stopped the latitude from going beyond `[-90,90]`, you won't have `cos_beta1` less than `0`.

> Username: adl1995  
> Created_at: 2019-06-02 12:46:42 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r289641039  

@vissarion - For the case when both `lat1` and `lon1` are 0 the problem seems to be originating from the `lambda12` function, specifically this line (https://github.com/BoostGSoC18/geometry/blob/feature/karney_inverse/include/boost/geometry/formulas/karney_inverse.hpp#L844).  
  
`sin_sigma2` and `cos_sigma2` are both set to 0, when in fact `cos_sigma2` should be close to 1. Upon a quick inspection, the computation for `lambda12` seems to be identical with GeographicLib, so the error is probably introduced before this function is called.  
  
> As long as you've stopped the latitude from going beyond [-90,90], you won't have cos_beta1 less than 0.  
  
@cffk - Is this condition imposed by `lon12` in GeographicLib? More specifically in line 177 of this file (https://geographiclib.sourceforge.io/html/Geodesic_8cpp_source.html)?

> Username: cffk  
> Created_at: 2019-06-02 14:46:22 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r289645595  

There's only a problem normalizing `sigma2` in `Lambda12` if `sbet2 = calp2 = 0`.  But these are equatorial geodesics which are handled separately (and `Lambda12` isn't called in this case).  I'm not sure why you're asking about `lon12`.  This of course (unlike the latitude) can be beyond `[-90, 90]`.

> Username: adl1995  
> Created_at: 2019-10-26 18:25:06 UTC  
> Updated_at: 2019-10-26 18:25:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r339310423  

Looking into this, the problem was introduced because `lat1` and `lat2` weren't being rounded off. Passing these values through `math::round_angle()` (https://github.com/boostorg/geometry/pull/500/commits/607f5c3be0cf520c0b9046ac7a78a44c321f949d) fixed the issue.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-13 12:46:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/500#pullrequestreview-145635356  

📁 include/boost/geometry/formulas/karney_inverse.hpp

```diff
 150 |+         // Make points close to the equator to lie on it.
 151 |+         lat1 = std::abs(lat1) > c90 ? math::NaN<CT>() : lat1;
 152 |+         lat2 = std::abs(lat2) > c90 ? math::NaN<CT>() : lat2;
```

> Username: awulkiew  
> Created_at: 2018-08-13 12:46:31 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r209594519  

`NaN()` for fundamental types may return `quiet_nan` or `max` depending on the compiler. For user-defined numeric types it will return 0. So this is not a good way to check this.  
  
But I'm confused. Why is it even needed here? AFAIU if `lat1` or `lat2` was NaN the result would be NaN as well. So why not simply return NaN here without performing all of the calculation below?  
  
Other options would be to:  
  - return some other value like numeric_limits::max (which will only work for fundamental types),  
  - return result outside valid bounds like negative distance,  
  - throw an exception.  
  
In fact we should be consistent across the whole library with handling of latitudes > 90. In all geographic strategies and projections the coordinates should be treated the same way. Why not simply move the coordinate to 90 or -90 deg? This would also handle cases when latitude was a result of some numerical operation and e.g. is slightly greater than 90 or below -90 (like `90.0000000001`).

> Username: cffk  
> Created_at: 2019-01-28 14:51:54 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r251446337  

The treatment of "out of bounds" latitudes is a policy decision which  
should apply to the library as a whole.  GeographicLib's policy is to  
allow arbitrary longitude and azimuth (with exact range reduction  
preformed as necessary) and to require that latitudes lie in the range  
[-90,90] deg.  
  
The latitude range is checked by the routines which handle user input  
and exceptions are thrown if needed.  With the lower level routines, out  
of range latitudes are silently converted to NaNs in the expectation  
that the resulting NaN will flag an obvious error to the user.  
  
This is prefered to a strategy of defining a protocol to convert the  
point to a legal one.  Note that the GeographicLib interface handles all  
angles in degrees.  So a latitude like 90.0000000001 will never by  
"accidentally" returned.  
  
There's no particular reason why the policy for Boost should match was  
GeographicLib does.

> Username: adl1995  
> Created_at: 2019-03-17 11:09:53 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r266234574  

@awulkiew - For the moment I have moved the coordinates to 90 deg when they are greater, and the test cases seem to pass.


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2018-08-13 12:50:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/500#pullrequestreview-145636879  

📁 include/boost/geometry/formulas/karney_inverse.hpp

```diff
 153 |+ 
 154 |+         lat1 = math::round_angle(lat1);
 155 |+         lat2 = math::round_angle(lat2);
```

> Username: awulkiew  
> Created_at: 2018-08-13 12:50:47 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r209595777  

Right, this is confusing. Round angle how exactly?  
  
This also begs various questions like. Why do NaN coordinates has to be rounded? Maybe this function handles NaN coordinates in some specific way so that it makes sense to still perform calculation below and not return right away? I know it doesn't but a person reading the code could be confused by this.

> Username: cffk  
> Created_at: 2019-01-28 14:53:13 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r251446936  

See my earlier comment (replying to "What does this function do exactly?").

> Username: adl1995  
> Created_at: 2019-03-17 11:09:56 UTC  
> Updated_at: 2019-10-26 18:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#discussion_r266234577  

This `math::round_angle()` function has been removed, and the original value is used without modification.


---

## Comment 6

> Username: vissarion  
> Created_at: 2019-02-06 15:00:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#issuecomment-461054059  

@adl1995 are you willing to answer the above comments so we can merge this piece of code?   
  
I am ok with merging if the above comments are somehow addressed.

---

## Comment 7

> Username: adl1995  
> Created_at: 2019-02-07 06:58:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#issuecomment-461308305  

@vissarion - Sorry for not being responsive on this PR.  
  
I will try and address the comments in the coming weeks so this can be merged.

---

## Comment 8

> Username: vissarion  
> Created_at: 2019-10-29 15:20:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#issuecomment-547475460  

Thanks Adeel for the long effort.   
  
NOTE: the operations are accurate (at least all the tests are passing) but I have noticed some performance issues, i.e. it is slower than `GeographicLib`, around 4 times in some quick benchmarks.     
  
We could merge now and resolve this issue in a different PR. @awulkiew @barendgehrels what do you think?

---

## Comment 9

> Username: awulkiew  
> Created_at: 2019-10-30 13:19:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/500#issuecomment-547898445  

@adl1995 Thanks!  
  
@vissarion Yes, I'm ok with merging it now.

---
