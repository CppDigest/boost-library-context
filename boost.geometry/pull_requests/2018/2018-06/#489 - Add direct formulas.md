# #489 Add direct formulas [Merged]

> Username: vissarion  
> Created at: 2018-06-12 13:08:27 UTC  
> Updated at: 2018-06-20 19:56:38 UTC  
> Merged at: 2018-06-20 18:16:57 UTC  
> Closed at: 2018-06-20 18:16:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/489  

This PR is a split from https://github.com/boostorg/geometry/pull/431 and contains 3 direct formulas, namely for the spherical case, the thomas first order formula for geographic CS (the direct equivalent for andoyer first order inverse formula) and a direct formula for the meridian geographic case.

---

## Review 1 [Changes requested]

> Username: awulkiew  
> Created_at: 2018-06-12 14:49:08 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/489#pullrequestreview-127983935  

📁 include/boost/geometry/formulas/elliptic_meridian_arc_direct.hpp

```diff
  42 |+         CT const f = formula::flattening<CT>(spheroid);
  43 |+         CT n = f / (CT(2) - f);
  44 |+         CT mp = 10001965.729;
```

> Username: awulkiew  
> Created_at: 2018-06-12 14:13:50 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r194753911  

Quarter meridian should be calculated using spheroid, here WGS84 is always used.

---

📁 include/boost/geometry/formulas/elliptic_meridian_arc_direct.hpp

```diff
  38 |+     // latitudes are assumed to be in radians and in [-pi/2,pi/2]
  39 |+     template <typename T, typename Spheroid>
  40 |+     static CT apply(T m, Spheroid const& spheroid)
```

> Username: awulkiew  
> Created_at: 2018-06-12 14:18:59 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r194756098  

What is the input and result of this formula? Why does it have different interface than the other direct formulas? AFAIU direct formula should take latitude, direction (north or south) and distance. The result should be the coordinates of the resulting point since they may be on the other side of the globe.  What am I missing here?

> Username: vissarion  
> Created_at: 2018-06-18 09:22:23 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r196013674  

That was just the formula for computation. I added the interface.


📁 include/boost/geometry/formulas/spherical.hpp

```diff
 221 | 
 222 |+ template <typename CT, typename Sphere>
 223 |+ inline result_direct<CT> spherical_direct(CT const& lon1,
```

> Username: awulkiew  
> Created_at: 2018-06-12 14:23:15 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r194757718  

An alternative formula would convert to 3D cartesian ECEF and calculate the result using vector algebra. This is how it's done in the spherical intersection and side strategies. So should be careful to not mix things. But AFAIU this formula is a building block for other geographic formulas and not used in spherical for now. Is my understanding correct?

> Username: vissarion  
> Created_at: 2018-06-18 09:28:44 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r196015433  

Yes this is not used anywhere for now, I added it for completeness. I could be used in geographic pt-seg distance formula for optimizations.

---

📁 include/boost/geometry/formulas/spherical.hpp

```diff
 256 |+     result.lon2 = lon1 + lon2 - omg1;
 257 |+     result.lat2 = lat2;
 258 |+     result.reverse_azimuth = alp2;
```

> Username: awulkiew  
> Created_at: 2018-06-12 14:25:42 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r194758652  

Are all of these results needed in all cases? In other formulas they are only calculated if compile-time flag is set. The same approach could be used here.


📁 include/boost/geometry/formulas/thomas_direct.hpp

```diff
  62 |-                                     Spheroid const& spheroid)
  62 |+                                     Spheroid const& spheroid,
  63 |+                                     bool SecondOrder = true)
```

> Username: awulkiew  
> Created_at: 2018-06-12 14:27:54 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r194759530  

Shouldn't this be template parameter?


📁 include/boost/geometry/formulas/thomas_first_order_direct.hpp

```diff
  58 |+                                     Azi const& azimuth12,
  59 |+                                     Spheroid const& spheroid)
  60 |+     {   return thomas_direct<
```

> Username: awulkiew  
> Created_at: 2018-06-12 14:45:31 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r194766643  

I propose to implement thomas as one struct taking `int Order` or `bool FirstOrder` as the first or second template parameter, e.g. simply as `thomas_direct`. And then derive both thomas 1st and 2nd order from that if needed. You can implement all of them in one file. If the order flag was struct template parameter you could simply derive from it without the need to implement the interface again.

> Username: vissarion  
> Created_at: 2018-06-18 11:41:14 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r196046851  

I followed a common interface solution since some formulas rely on it e.g. https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/formulas/gnomonic_intersection.hpp#L39  
  
Now changed it to (the more convenient, I agree) interface with SecondOrder as template parameter and use strategies (in particular formula policies) for gnomonic_intersection in tests https://github.com/boostorg/geometry/pull/489/files#diff-faacdab3bb307b84e30e9bde5ca88591R74 However, this should be fixed in the future. Maybe by just moving formulas policies into formulas?


📁 include/boost/geometry/strategies/geographic/distance.hpp

```diff
  24 | #include <boost/geometry/formulas/andoyer_inverse.hpp>
  25 |- #include <boost/geometry/formulas/elliptic_arc_length.hpp>
  25 |+ #include <boost/geometry/formulas/elliptic_meridian_arc_inverse.hpp>
```

> Username: awulkiew  
> Created_at: 2018-06-12 14:48:38 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r194768162  

Btw, for consistency, shouldn't it be called "spheroidal meridian arc"? Are we ok with using "spheroid" and "ellipsoid" interchangeably? It would probably only be a problem if we wanted to support 3-axial ellipsoids.

> Username: vissarion  
> Created_at: 2018-06-18 09:52:57 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r196022260  

ok for now I simply use the meridian_direct and meridian_inverse


---

## Review 2 [Changes requested]

> Username: awulkiew  
> Created_at: 2018-06-13 15:17:00 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/489#pullrequestreview-128426992  

📁 include/boost/geometry/formulas/quarter_meridian.hpp

```diff
  75 |+         do
  76 |+         {
  77 |+             result = result * x + *--it;
```

> Username: awulkiew  
> Created_at: 2018-06-13 15:13:07 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r195123341  

What if `begin == end`? Either return 0 or check assertion.

---

📁 include/boost/geometry/formulas/quarter_meridian.hpp

```diff
  57 |+                                 + get_radius<2>(geometry)) / c4;
  58 |+         return geometry::math::pi<ResultType>() * ab4 *
  59 |+                  horner_evaluate(n*n, C, C+8) / C[0];
```

> Username: awulkiew  
> Created_at: 2018-06-13 15:16:09 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r195124548  

So this function will calculate 8 nested levels of products of products or products, etc. My guess is that the error propagation will be big. Is this a problem? Would representing this series in a "typical" way improve the accuracy of the result, i.e. as sum of ratios multiplied by x^i?

> Username: vissarion  
> Created_at: 2018-06-14 07:21:03 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r195323237  

First, using horner rule (i.e. nested evaluation) should be faster than the standard evaluation, the first requires 2n operations while the second 3n-1 (if powers are computed iteratively). Second, horner seems to me more accurate since it uses less operations. Third, if this function is placed somewhere in geometry::math or in utilities then it could be used wherever we have to evaluate some polynomial. This will make the code shorter and more elegant than implementing manually polynomial evaluations in standard form when it is needed.


---

## Comment 3

> Username: vissarion  
> Created_at: 2018-06-18 12:02:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#issuecomment-398031284  

@awulkiew  Thanks for the comments. I addressed them all by commits or comments.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2018-06-18 18:20:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#issuecomment-398148453  

Thanks! I'm ok with merging.

---

## Review 5 [Changes requested]

> Username: awulkiew  
> Created_at: 2018-06-18 18:26:13 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/489#pullrequestreview-129682893  

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 331 |             end = begin + SeriesOrder + 1 - i;
 332 |-             coeffs2[i] = ((i==0) ? CT(1) : pow(var2,int(i)))
 332 |+             coeffs2[i] = ((i==0) ? CT(1) : pow(var2, CT(i)))
```

> Username: awulkiew  
> Created_at: 2018-06-18 18:26:07 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r196178885  

test/algorithms/area/area.cpp:216:22:   required from here  
    ../../boost/geometry/formulas/area_formulas.hpp:332:59: error: no matching function for call to ‘pow(double&, double)’  
                 coeffs2[i] = ((i==0) ? CT(1) : pow(var2, CT(i)))  
                                                               ^

> Username: awulkiew  
> Created_at: 2018-06-18 18:31:04 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r196180440  

In various places in `area_formulas.hpp` variables are passed by value instead of `const&`. May this be the cause?

> Username: vissarion  
> Created_at: 2018-06-19 09:53:08 UTC  
> Updated_at: 2018-06-19 10:08:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r196361259  

I use pass by reference but I get the same error in circleci. Note that this PR does not touch area_formulas.hpp i.e. the error also appear in https://github.com/boostorg/geometry/pull/489/commits/12f7a2239bb3dbec768fa7b80ebca0c40512b77b It seems that the error is in calling boost `pow` function from `boost/rational.hpp` although I cannot find `pow` function there and I cannot reproduce the error in my pc with `gcc-4.9` and `debug` mode.

> Username: vissarion  
> Created_at: 2018-06-20 07:17:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r196670041  

@awulkiew tried with the std pow function in area and now pow functions in projections yield errors see: https://circleci.com/gh/vissarion/geometry/249#tests/containers/1  
  
Did you have similar problems in other branches?

> Username: vissarion  
> Created_at: 2018-06-20 08:15:43 UTC  
> Updated_at: 2018-06-20 08:15:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r196684927  

Ok there is a general issue https://github.com/boostorg/geometry/issues/491 with pow function independent from this PR.

> Username: awulkiew  
> Created_at: 2018-06-20 15:59:14 UTC  
> Updated_at: 2018-06-20 16:03:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r196838928  

@vissarion right, so this should be fixed library-wise at our side. AFAIK the correct way of fixing it is bringing std namespace into the scope and then calling `pow()` so:  
  
    using namespace std;  
    pow(1, 2);  
  
This way the compiler should choose the correct one. And actually we could have our own function doing exactly that (in util/math.hpp):  
  
    namespace geometry { namespace math {  
      
    template <typename T>  
    T pow(T const& v1, T const& v2)  
    {  
        using namespace std;  
        return pow(v1, v2);  
    }  
  
    }}  
  
and then call:  
  
    math::pow(v1, v2);

> Username: awulkiew  
> Created_at: 2018-06-20 16:07:08 UTC  
> Updated_at: 2018-06-20 16:11:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#discussion_r196842733  

If it didn't work the function could be brought into the scope. This is how it's done in Boost.Math (https://github.com/boostorg/math/blob/develop/include/boost/math/tools/config.hpp#L308):  
  
    using std::pow;  
    pow(1, 2);  
  
This is probably better since we're not enabling the whole namespace.


---

## Comment 6

> Username: awulkiew  
> Created_at: 2018-06-20 16:21:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/489#issuecomment-398810831  

I'm ok with merging it now and fixing `pow()` afterwards. Since the problem is mainly in projections I'll fix it as described above, by implementing `geometry::math::pow()`.

---
