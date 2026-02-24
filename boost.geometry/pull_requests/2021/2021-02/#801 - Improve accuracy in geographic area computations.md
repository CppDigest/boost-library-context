# #801 Improve accuracy in geographic area computations [Merged]

> Username: vissarion  
> Created at: 2021-02-01 15:09:39 UTC  
> Updated at: 2021-03-02 14:50:56 UTC  
> Merged at: 2021-03-02 14:50:47 UTC  
> Closed at: 2021-03-02 14:50:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/801  

This PR proposes a fix for https://github.com/boostorg/geometry/issues/799   
  
In short this PR  
  
- enables the series expansions formulas and make them available for area computations  
- changes the spherical part of the geogrpahic area computation  
- adds an error threshold in the geographic area computation and when the ellipsoidal part is far from the spherical part (indicates a possible inaccuracy in azimuth computation) discards the ellipsoidal part. This is a heuristic that improves the accuracy in many cases.  
- adds tests

---

## Review 1 [Commented]

> Username: CJxD  
> Created_at: 2021-02-01 15:19:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/801#pullrequestreview-580458422  

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 469 |-         if (!meridian && cos_omg12 > -CT(0.7)
 470 |-                       && sin_bet2 - sin_bet1 < CT(1.75)) // short segment
 474 |+         if (!meridian && cos_omg12 > -CT(0.7071)
```

> Username: CJxD  
> Created_at: 2021-02-01 15:19:02 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r567904516  

Is this supposed to be 1/sqrt(2)? Perhaps use a constant for this?

> Username: vissarion  
> Created_at: 2021-02-05 10:30:41 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r570871224  

You are right, this is to select between trapezoid formula and azimuth subtraction (i.e. alp2 - alp1) for spherical excess computation.   
  
With trapezoid formula we have the following relative errors (using the dataset from https://zenodo.org/record/32156#.WOyaMrUmv7C  
  
maximum legnth | 2 | 10 | 100 | 1000 | 2000 | 1000000 | 2000000 | 5000000 | 7500000 | 10000000 | 12500000 | 15000000 | 17500000 | 19500000 | 20000000  
-- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | --  
andoyer | 0.002051124711 | 5.00E-06 | 1.80E-07 | 6.16E-06 | 1.30E-09 | 1.37E-05 | 5.49E-05 | 0.0003688702402 | 0.0009236612365 | 0.001903669355 | 0.003755516045 | 0.007758503031 | 0.01998210139 | 0.1029663252 | 0.4328622397  
thomas | 0.004475880816 | 2.88E-10 | 3.86E-09 | 3.85E-10 | 1.61E-10 | 1.37E-05 | 5.49E-05 | 0.0003688700554 | 0.0009236610226 | 0.00190366909 | 0.003755515698 | 0.007758502504 | 0.0199821028 | 0.102966375 | 0.4329122004  
vincenty | 0.004729452869 | 4.34E-06 | 1.63E-07 | 1.53E-07 | 1.52E-07 | 1.36E-05 | 5.49E-05 | 0.0003689026972 | 0.0009236909016 | 0.001903649421 | 0.003755454646 | 0.007757974129 | 0.0199798291 | 0.1029193871 | 0.4322189917  
  
While with azimuth subtraction (i.e. alp2 - alp1)  
  
maximum legnth | 2 | 10 | 100 | 1000 | 2000 | 1000000 | 2000000 | 5000000 | 7500000 | 10000000 | 12500000 | 15000000 | 17500000 | 19500000 | 20000000  
-- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | --  
andoyer | 0.9997504932 | 0.001255017513 | 7.88E-05 | 0.00214284374 | 9.84E-07 | 2.65E-10 | 5.72E-09 | 1.49E-09 | thomas | 1.09E-08 | 5.73E-09 | 4.34E-11 | 1.50E-10 | 3.42E-11 | 1.60E-10 | 0.06630115469  
vincenty| 1 | 1.69E-07 | 6.84E-06 | 8.66E-06 | 5.48E-08 | 3.78E-08 | 3.83E-08 | 4.13E-08 | 4.77E-08 | 5.89E-08 | 9.15E-08 | 3.70E-07 | 3.22E-06 | 0.0002948330798 | 0.214268049  
1.689545791 | 0.001697061092 | 9.38E-05 | 8.60E-06 | 1.59E-07 | 1.67E-07 | 3.23E-07 | 2.04E-06 | 5.61E-06 | 1.37E-05 | 3.45E-05 | 0.0001037443284 | 0.0005077352782 | 0.01102688439 | 0.1776476855  
  
So we want to keep the best of both worlds which is not very simple by using the trigonometric thresholds and since we have already computed the azimuths in a previous step of the computation, we can compute the distance too and use it as a threshold. Thus, we have to select the former formula for segments with length < 2000 and the latter for the rest. In lack of data between 2000 and 1000000 segment lengths I am choosing 10000 as a threshold.


---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2021-02-03 11:30:18 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/801#pullrequestreview-582263543  

Thanks, in general I'm OK, few questions

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 509 | 
 510 |-         if(ExpandEpsN){ // expand by eps and n
 510 |+         if (ExpandEpsN){ // expand by eps and n
```

> Username: barendgehrels  
> Created_at: 2021-02-03 11:26:49 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r569337089  

Minor - since you corrected the format, the `{` should be changed too


📁 include/boost/geometry/strategy/geographic/area.hpp

```diff
 130 |+             return_type spherical_term = spheroid_const.m_c2 * m_excess_sum;
 131 |+             return_type ellipsoidal_term = spheroid_const.m_e2
 132 |+                 * spheroid_const.m_a2 * m_correction_sum;
```

> Username: barendgehrels  
> Created_at: 2021-02-03 11:28:01 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r569337897  

Maybe you can make them `const`

> Username: vissarion  
> Created_at: 2021-02-05 10:33:25 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r570872688  

sure


📁 test/algorithms/area/area_geo.cpp

```diff
  96 |     area = bg::area(geometry_geo, geographic_andoyer5);
  97 |-     BOOST_CHECK_CLOSE(area, 63316309224411.094, err);
  97 |+     BOOST_CHECK_CLOSE(area, 63316423410701.602, err);
```

> Username: barendgehrels  
> Created_at: 2021-02-03 11:28:50 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r569338430  

Looks cool.  
I'm just wondering, how do we know it's better?

> Username: vissarion  
> Created_at: 2021-02-05 15:13:07 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r571037795  

it is with respect to geographiclib that gives `63316536351834.289`  
  
I added also comparisons with geographiclib in `area_sph_geo.cpp` tests showing that BG after this PR is more accurate than before.


📁 test/algorithms/distance/distance_geo_linear_box.cpp

```diff
 565 |     test_all_l_ar<geo_point>(thomas_pp(), thomas_ps(), thomas_sb());
 566 |     test_all_l_ar<geo_point>(andoyer_pp(), andoyer_ps(), andoyer_sb());
 567 |+     //test_all_l_ar<geo_point>(karney_pp(), karney_ps(), karney_sb());
```

> Username: barendgehrels  
> Created_at: 2021-02-03 11:29:47 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r569338995  

Why are these added and commented? Still to do?

> Username: vissarion  
> Created_at: 2021-02-05 20:08:43 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r571221712  

Yes, actually only `karney_pp()` strategy is working after activating the series formulas. This should be fixed in another PR.


---

## Comment 3

> Username: vissarion  
> Created_at: 2021-02-05 20:12:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#issuecomment-774262556  

@barendgehrels @CJxD thanks for your comments.

---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2021-02-17 13:01:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/801#pullrequestreview-592167965  

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 429 |-                                                    get_as_radian<1>(p2),
 430 |-                                                    spheroid_const.m_spheroid);
 431 |+         auto const i_res = FormulaPolicy::template inverse
```

> Username: awulkiew  
> Created_at: 2021-02-17 13:01:21 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r577591481  

FormulaPolicy is strategies-related. It was implemented in order to allow t pick formula in strategies. They are implemented in strategies directory. I think bare formula should be used here.

> Username: vissarion  
> Created_at: 2021-02-18 11:09:02 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r578328868  

I am aware that `FormulaPolicy` was implemented to pick formulas in strategies and we are in formulas here, but I found it handy since base formula cannot be passed here since karney inverse formula has a different templated type i.e.   
`inverse<typename CT, bool, bool, bool, bool, bool, size_t >` while the other inverse formulas have `inverse<typename CT, bool, bool, bool, bool, bool>`

> Username: awulkiew  
> Created_at: 2021-02-18 14:39:33 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r578470458  

If you instantiate it here using FormulaPolicy the default `SeriesOrder = 8` of karney's formula is used anyway. The interface of formulas and policies are the same, you still have to pass the same parameters because other formulas does no take `SeriesOrder`.  
  
So this is not about passing `SeriesOrder` but only about the template parameter.  
  
So why not simply have a `detail` version allowing to pass `SeriesOrder` for testing purposes and then the "official" version implementing the same interface as all other formulas?  
  
Btw, there is also the `SeriesOrder` passed into the strategy and then into this area formula by the user. Are they corresponding? Can there be various different orders used at the same time? If this is the case shouldn't we simply remove them and always use our defaults designed for specific `FormulaPolicy`?

> Username: vissarion  
> Created_at: 2021-02-22 17:01:49 UTC  
> Updated_at: 2021-02-22 17:01:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r580418584  

> So why not simply have a detail version allowing to pass SeriesOrder for testing purposes and then the "official" version implementing the same interface as all other formulas?  
  
This is a good idea. I implemented a `detail` version of `karney_inverse`.   
  
I kept the interface of area strategy the same. `SeriesOrder` in area strategy are unrelated to series order in inverse formulas. I added some examples in area tests to show this.


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2021-02-17 13:21:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/801#pullrequestreview-592185455  

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 469 |-         if (!meridian && cos_omg12 > -CT(0.7)
 470 |-                       && sin_bet2 - sin_bet1 < CT(1.75)) // short segment
 481 |+         if (!meridian && (i_res.distance) < 10000)  // short segment
```

> Username: awulkiew  
> Created_at: 2021-02-17 13:21:58 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r577604794  

Two problems with it:  
- it's looks like an arbitrary number based on an (informed) guess but instead should probably be empirically found by experimenting  
- it should at least depend on the size of the globe, it is possible to create a spheroid using km instead of meters for axes, in such case this number should probably be `10`, not to mention that the flattening is probably also a factor

> Username: vissarion  
> Created_at: 2021-02-18 10:53:42 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r578319127  

> it's looks like an arbitrary number based on an (informed) guess but instead should probably be empirically found by experimenting  
  
see https://github.com/boostorg/geometry/pull/801#discussion_r570871224 above, the experiments are with WGS84, of course one could experiment more, but for the purpose of this PR I think is enough  
  
> it should at least depend on the size of the globe, it is possible to create a spheroid using km instead of meters for axes, in such case this number should probably be 10, not to mention that the flattening is probably also a factor  
  
right, I replaced `10000` with `mean_radius<CT>(spheroid_const.m_spheroid) / CT(638)`


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2021-02-17 14:59:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/801#pullrequestreview-592285943  

📁 include/boost/geometry/formulas/karney_inverse.hpp

```diff
 465 |             {
 468 |-                 result.azimuth = atan2(sin_alpha1, cos_alpha1) * r2d;
 466 |+                 result.azimuth = atan2(sin_alpha1, cos_alpha1);
```

> Username: awulkiew  
> Created_at: 2021-02-17 14:59:29 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r577681446  

Was this a bug? A leftover from the previous version returning degrees?

> Username: vissarion  
> Created_at: 2021-02-18 11:12:59 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r578331431  

Probably the second.


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2021-02-17 15:00:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/801#pullrequestreview-592287449  

📁 include/boost/geometry/strategies/geographic/parameters.hpp

```diff
 180 | };
 181 |- */
 181 |+ 
```

> Username: awulkiew  
> Created_at: 2021-02-17 15:00:43 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r577682648  

Ok, so this also enables karney formula policy.


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2021-02-17 15:02:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/801#pullrequestreview-592289122  

📁 include/boost/geometry/strategy/geographic/area.hpp

```diff
 151 |+             // ignore ellipsoidal term if is large (probably from an azimuth
 152 |+             // inaccuracy)
 153 |+             return_type sum = math::abs(ellipsoidal_term/spherical_term) > 0.01
```

> Username: awulkiew  
> Created_at: 2021-02-17 15:02:12 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r577683931  

Here is another magic number. If it's from azimuth inaccuracy shouldn't it be based on formula?

> Username: vissarion  
> Created_at: 2021-02-18 11:16:28 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r578333528  

It is independent from the choice of formula, i.e. for an accurate formula this condition should always be false. It describes  how  tolerate we are with errors in inaccurate formulas.


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2021-02-17 15:06:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/801#pullrequestreview-592294422  

📁 include/boost/geometry/strategy/geographic/area.hpp

```diff
  82 |     {
  83 |+         // Area formula is implemented for a maximum series order 5
  84 |+         static constexpr auto SeriesOrderNorm = SeriesOrder > 5 ? 5 : SeriesOrder;
```

> Username: awulkiew  
> Created_at: 2021-02-17 15:06:52 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r577687919  

Why not define it in the class scope together with other static const variables?

> Username: vissarion  
> Created_at: 2021-02-18 11:21:34 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r578336723  

thanks I overlooked it!


---

## Review 10 [Commented]

> Username: awulkiew  
> Created_at: 2021-02-17 15:07:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/801#pullrequestreview-592295142  

📁 include/boost/geometry/strategy/geographic/area.hpp

```diff
 203 |         {
 204 |+             // Area formula is implemented for a maximum series order 5
 205 |+             constexpr auto SeriesOrderNorm = SeriesOrder > 5 ? 5 : SeriesOrder;
```

> Username: awulkiew  
> Created_at: 2021-02-17 15:07:32 UTC  
> Updated_at: 2021-02-22 16:59:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/801#discussion_r577688443  

This would not be needed if it was defined as static const class member.


---
