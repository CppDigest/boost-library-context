# #359 Feature/area [Merged]

> Username: vissarion  
> Created at: 2016-08-17 12:02:38 UTC  
> Updated at: 2017-03-09 11:02:12 UTC  
> Merged at: 2017-02-01 23:49:34 UTC  
> Closed at: 2017-02-01 23:49:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/359  

# Algorithms for spherical and geographic area  
## Problem definition  
  
 Given a spherical or geographic polygon we want to approximate its area.  
## What is already implemented  
  
An algorithm for spherical polygons using the L' Huilier formula (see https://en.wikipedia.org/wiki/Spherical_trigonometry#Area_and_spherical_excess). There are some issues reported for this implementation: https://svn.boost.org/trac/boost/ticket/11930 and https://svn.boost.org/trac/boost/ticket/11931  
## What this PR implements  
  
Algorithms for spherical and geographic polygons. It uses the trapezoidal formula (see: two last formulas in https://en.wikipedia.org/wiki/Spherical_trigonometry#Area_and_spherical_excess) for computing the spherical area and series expansion for approximating the integral that gives the ellipsoidal correction used for the geographic area.  
### Spherical area  
  
Testing trapezoidal(TR) vs huiller(HU). We perform 1M iterations on 5 different polygons   
  
>  "POLYGON((52 0, -26 28, -23 -43, 41 -74, 52 0))"  
>  "POLYGON((0 0, 0  45, 45 0, 0 0))"  
>  "POLYGON((10.10 10.10,  10.11 10.10, 10.11 10.11, 10.10 10.11, 10.10 10.10))",  
>  "POLYGON((0 0, 0 90, 90 0, 0 0))"  
>  "POLYGON((0 89, 90 89, 180 89, 270 89, 0 89))"  
  
and we get  
  
> TR = 0.07 sec  
> HU = 0.85 sec  
  
This test gives evidence that HU strategy is redundant (unless if one finds an instance where HU is faster).  
  
The new implementation fixes all the issues reported above.  
### Geographic area  
  
Geographic area computation needs the azimuth computation. Hence, the algorithm can be parametrized by some azimuth computation strategy. It can also be parametrized by the order of approximation for the series expansion.  
  
Here we try the following strategies:    
- andoyer_inverse (AD)  
- thomas_inverse (TH)  
- vincenty_inverse (VI)  
  
Find below the timings (100K iterations, times in seconds) and area values for   
  
> POLYGON((52 0, 41 -74, -23 -43, -26 28, 52 0))   
  
for different strategies and orders of approximation (from order 1 to 5).  
  
```  
method    time          area  
-------------------------------------------------------  
AD1        0.329        63292982421447.3125  
AD2        0.337        63292982299473.6328125  
AD3        0.371        63292982299578.328125  
AD4        0.411        63292982299578.2265625  
AD5        0.445        63292982299578.2265625  
TH1        0.395        63292982179555.765625  
TH2        0.419        63292982057581.7109375  
TH3        0.439        63292982057686.40625  
TH4        0.480        63292982057686.3046875  
TH5        0.512        63292982057686.3046875  
VI1        0.512        63292982178432.859375  
VI2        0.522        63292982056458.8046875  
VI3        0.557        63292982056563.5  
VI4        0.599        63292982056563.3984375  
VI5        0.632        63292982056563.40625  
```  
  
**Note:** GeographicLib needs 0.628 secs and gives 63316536351834.289. MS SQL Server gives 632930207487035.  
  
**Note:** There are some issues with ttmath. In some cases NaN is returned. This is probably because tan() function is implemented as sin()/cos() in ttmath library.   
### **_EDIT**_  
  
A more accurate way for computing the spherical term is added (commit c9d6796b65c244aa962ab602dd01c400718e1fb8). Moreover, the azimuth computation is performed only once in area_formulas::ellipsoidal(), new performance tests follows:  
  
```  
method    time          area  
-------------------------------------------------------  
AD1        0.21        63316309346280.20  
AD2        0.22        63316309224306.50  
AD3        0.26        63316309224411.20  
AD4        0.29        63316309224411.10  
AD5        0.33        63316309224411.10  
TH1        0.24        63316536214315.30  
TH2        0.25        63316536092341.30  
TH3        0.29        63316536092446.00  
TH4        0.33        63316536092445.90  
TH5        0.35        63316536092445.90  
VI1        0.29        63316536473799.00  
VI2        0.31        63316536351824.90  
VI3        0.34        63316536351929.60  
VI4        0.38        63316536351929.50  
VI5        0.42        63316536351929.50  
```  
  
**Note:** Results are now closer to ones from GeographicLib and runtime is faster than before.  
  
Test with a smaller polygon:   
  
> POLYGON((10.10 10.10, 10.11 10.15, 10.15 10.15, 10.13 10.12, 10.10 10.10))  
  
```  
method    time          area  
-------------------------------------------------------  
AD1        0.21        15176024.95  
AD2        0.23        15176024.41  
AD3        0.26        15176024.42  
AD4        0.29        15176024.42  
AD5        0.32        15176024.42  
TH1        0.24        15152760.69  
TH2        0.25        15152760.16  
TH3        0.28        15152760.16  
TH4        0.32        15152760.16  
TH5        0.36        15152760.16  
VI1        0.29        15152761.56  
VI2        0.30        15152761.02  
VI3        0.33        15152761.02  
VI4        0.37        15152761.02  
VI5        0.40        15152761.02  
```  
  
GeographicLib returns 15152760.51

---

## Comment 1

> Username: awulkiew  
> Created_at: 2016-08-17 15:27:32 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75145569  

It's const even without `const` keyword.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2016-08-17 15:28:12 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75145697  

Bracket should go into the next line.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2016-08-17 15:36:20 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75147280  

`do`-`while` loop is not covered in guidelines (https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers) but considering how `if-else` and `for` loop should be defined I think it should rather look like this, even though it's ugly ;) :  
  
```  
do  
{  
    /*...*/  
}  
while (...);  
```  
  
Barend do you confirm?

---

## Comment 4

> Username: awulkiew  
> Created_at: 2016-08-17 15:39:12 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75147857  

Why not `bool odd = true;`?

---

## Comment 5

> Username: awulkiew  
> Created_at: 2016-08-17 15:44:13 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75148819  

Is this a call of std function hypot? If that's the case then it cannot be used like this since it was introduced in C++11. You should add your own implementation in `utils/math.hpp` falling back to `std::hypot()` in C++11. An alternative would be Boost.Math special function hypot().

---

## Comment 6

> Username: awulkiew  
> Created_at: 2016-08-17 16:16:20 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75155077  

`Spheroid` is not needed here. `spheroid_constants` have `CT` type anyway. The constructor could take `Spheroid` as template parameter.  
  
`const` keyword is not needed here.  
  
Now the most tricky part.  
- `Strategy` is not a strategy in the Boost.Geometry meaning so it should be renamed if we decided to pass it. It'd rather be something like `Policy` (we have several) or `InverseFormula`.  
- Inverse solution formulas take `bool` parameters enabling various parts of computation so by taking the formula type we're relying on the user to create proper type, i.e. enabling azimuths computation. So because of that, and for convenience instead of a type `typename InverseFormula` a template could be required here `template <typename, bool, bool, bool, bool ,bool> class InverseFormula`.  
- I'm not sure if it's a good idea to allow/force the user to pass formulas into strategies. I'd rather hide them (i.e. not expose in the "official" interface) and use only internally, i.e. treat them like defined in `detail` namespace. This way we could safely change their interface if needed.  
  
So I see two possibilities:  
1. Implement several strategies: area_andoyer, area_thomas, area_vincenty  
2. Take only `size_t ApproximationOrder` or some `typename ApproximationTag`. Then in compile time instantiate corresponding inverse formula and use corresponding order of series expansion. As a side note, the problem with `ApproximationOrder` is that we could have several methods using the same order. And `ApproximationTag` would be less convenient for the user than solution 1.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2016-08-17 16:22:45 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75156239  

So if template was passed above instead of a type like this:  
  
```  
template <typename, bool, bool, bool, bool ,bool> class InverseFormula = geometry::formula::thomas_inverse  
```  
  
this would look like this:  
  
   typedef InverseFormula<CT, false, true, true> azimuth_formula;  
  
If we decided to get rid of `InverseFormula`/`Policy` then here a formula type would be instantiated based on the `Order` or `Tag`.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2016-08-17 16:23:32 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75156373  

This is not needed if Spheroid is taken as template parameter of the constructor.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2016-08-17 16:24:21 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75156506  

A space between `if` and `(` is required according to BG guidelines.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2016-08-17 16:25:02 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75156615  

```  
}  
else  
{  
```

---

## Comment 11

> Username: awulkiew  
> Created_at: 2016-08-17 16:26:09 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75156825  

Here too.

---

## Comment 12

> Username: awulkiew  
> Created_at: 2016-08-17 16:26:46 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75156939  

Is this work in progress or not needed?

---

## Comment 13

> Username: awulkiew  
> Created_at: 2016-08-17 16:33:30 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75158187  

This is how the older spherical strategy works, i.e. takes the radius. Should we be backward compatible or should we be consistent with geographic strategy taking SRS model, i.e. Sphere?

---

## Comment 14

> Username: awulkiew  
> Created_at: 2016-08-17 16:37:13 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75158850  

Similar situation as in the case of `hypot()`. We should implement it by ourselves or use Boost.Math and in C++11 use std version.

---

## Comment 15

> Username: vissarion  
> Created_at: 2016-08-18 10:33:47 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75284981  

Both :) It is work in progress but it is not needed if we decide to get rid of l'Hulier's formula.

---

## Comment 16

> Username: vissarion  
> Created_at: 2016-08-18 11:00:27 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75288166  

Ok. Just note that there is a problem with `ttmath` and `atanh()` from Boost.Math.

---

## Comment 17

> Username: awulkiew  
> Created_at: 2016-08-18 12:42:55 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75300192  

What is this problem?  
Anyway, `bg::math::atanh()` could be written in a way allowing to use ttmath's `atanh()` if defined instead ot STD version in C++11 or Boost.Math version in C++98 by use of `using` directive to bring a function into the scope. See e.g. how `bg::sqrt()` is defined: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/math.hpp#L262

---

## Comment 18

> Username: awulkiew  
> Created_at: 2016-08-18 13:40:36 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75309135  

I'm taking back a remark about `Spheroid` for now. It's because in existing geographic strategies the `Spheroid` parameter exists. On the other hand there are no `Point` parameters, but that's because the existing strategies are distance and side strategies which might take two different `Point` types.  
  
Therefore we could think about getting rid of `PointOfSegment`. Unfortunately `area()` algorithm have to store the state of area calculation so the existing area strategies define this type and `PointOfSegment` is needed for that. So if we wanted to get rid of `PointOfSegment` we'd have to modify the `area()` algorithm and existing area strategies. They'd have to define a template allowing the algorithm to figure out the strategy sumation state type from `Point`/`Geometry` (in C++11 it won't be needed) but this would be done on algorithm level, not user level. So this would be a breaking change. On the other hand area strategies would be consistent with other strategies not requireing `Point` template parameters.  
  
EDIT: But if we agreed to do this it shouldn't be a part of this PR so no need to change it now.

---

## Comment 19

> Username: vissarion  
> Created_at: 2016-08-19 11:00:35 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75463723  

I added a new constructor for consistency and also kept one for some backward compatibility. However, the default is set to srs sphere.

---

## Comment 20

> Username: awulkiew  
> Created_at: 2016-08-19 11:39:40 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r75467673  

If the default use `srs::sphere` then the strategy is not backward compatible, because `srs::sphere` by default has radius equal to mean radius of WGS84 spheroid `(2a + b) / 3`. We could leave the old default as it was. Barend what do you think?

---

## Comment 21

> Username: vissarion  
> Created_at: 2016-08-19 12:40:07 UTC  
> Updated_at: 2016-08-23 09:03:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#issuecomment-241007545  

There is also the orientation issue in the semantics of polygon area. There are two choices when we invert the orientation of a polygon: (i) compute the area negated or (ii) compute the area of the compliment. I would go for (i) since (ii) can be easily obtained from it by adding the area of the sphere or spheroid.

---

## Comment 22

> Username: vissarion  
> Created_at: 2016-08-31 15:46:55 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r77013496  

I changed the interface so now the strategy can be defined as `area_geographic <PointType, InverseFormula, Order>` where InverseFormula can be one of `bg::formula::vincenty_inverse, bg::formula::thomas_inverse, bg::formula::andoyer_inverse` and Order a number in [0,5].

---

## Comment 23

> Username: barendgehrels  
> Created_at: 2016-12-01 17:25:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#issuecomment-264236708  

This is already open for quite some time - is it waiting for me?

---

## Comment 24

> Username: vissarion  
> Created_at: 2016-12-02 10:13:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#issuecomment-264419978  

Yes, Adam has already reviewed it.

---

## Comment 25

> Username: awulkiew  
> Created_at: 2017-01-16 01:40:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#issuecomment-272755611  

Yes, I reviewed it and I'm ok with merging.  
  
I'm not sure what should be the template parameters, i.e. if passing formulas directly into the strategy is a good idea. This is how it's done in other newly added strategies as well. So later we could think about some different, consistent interface e.g. taking formula tag defined in `bg::strategies` namespace. Older geographic strategies (distance and side) are different because for each formula there is separate strategy (`strategy::distance::andoyer`, `strategy::distance::thomas`, etc.), so to be consistent we should also implement it as one e.g. `distance::geodesic` and `side::geodesic` strategy taking formula or tag as template parameter, as any other strategy.  
  
As we're at strategies' names. Is `area_geographic` a good name? Maybe we should be more explicit? In case we wanted to implement additional geographic area strategy in the future. Plus, it's already in namespace `area`, full name is `bg::strategy::area::area_geographic`, so `area_` prefix is redundant. What do you think about giving it a name of an author or algorithm? Or what do you think about naming all strategies treating segments as fragments of geodesics as `geodesic`, so `distance::geodesic`, `area::geodesic`, etc.?

---

## Comment 26

> Username: vissarion  
> Created_at: 2017-01-16 11:03:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#issuecomment-272832399  

I agree with the first paragraph. Regarding names, I would go for giving names of algorithms. In the current case we use the trapezoidal algorithm in a geographic system using series expansion for approximating the integrals, so e.g. `bg::strategy::area::trapezoidal_geographic_series` could be a name.

---

## Comment 27

> Username: awulkiew  
> Created_at: 2017-01-16 17:10:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#issuecomment-272917225  

I doubt that the majority of the users care about the internals of a strategy. Forcing the user to know the exact name of geographic strategy in case when he/she only needs to pass spheroid model is not a good idea. If I was a user I'd like to have some convenient name to deal with. Maybe the one you've choosen but without the redundant `area_` prefix? So `area::geographic`, basically the name of the CS. Then we could rename strategies of other algorithms the same way, so `distance::geographic`, `side::geographic`, etc. and this would be consistent and clear for the user.  
  
If in the future we choose to implement additional strategy and/or change template parameters we could simply change names or add strategies with specific names and use one of them (derive) in the convenient/default `area::geographic` strategy. Then simply inform the users about the breaking change. But to be honest I don't think additional geographic strategies will be implemented anytime soon so for now we only need one convenient name.  
  
What do you think? Barend do you have a preference?

---

## Comment 28

> Username: vissarion  
> Created_at: 2017-01-27 17:42:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#issuecomment-275726198  

I agree, renamed to area::geographic.

---

## Comment 29

> Username: awulkiew  
> Created_at: 2017-01-28 13:19:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#issuecomment-275847760  

@barendgehrels Are you ok with merging?

---

## Review 30 [Commented]

> Username: barendgehrels  
> Created_at: 2017-01-28 19:57:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/359#pullrequestreview-18979957  

📁 include/boost/geometry/algorithms/detail/calculate_sum.hpp

```diff
  36 |     {
  33 |-         ReturnType sum = ReturnType();
  37 |+         ReturnType sum = ReturnType(0);
```

> Username: barendgehrels  
> Created_at: 2017-01-28 19:57:51 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r98338163  

Oh, it was actually OK as it was. You may initialize a template parameter using ReturnType(). It gets the zero automatically. That is made for templates. For complex types (such as high precision), it is often more convenient and does not need a conversion from 0 to the type (or it might even be impossible).

> Username: awulkiew  
> Created_at: 2017-01-28 21:45:24 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r98339920  

Do you have some example where this is a problem?  
  
For scalar types there is no problem. But for classes with user defined default constructor this constructor will be called. And it is possible that the type will not be initialized properly or will be initialized to some unexpected value like NaN in the constructor. Like in the case of the original ttmath type. This is why you had to implement some workarounds for this library in the past. I think that if an object has to be initialized explicitly to store some specific value then it should be explicitly stated in the code. So it's ok as it is or it could be even simpler:  
  
    ReturnType sum = 0;  
  
It'll compile if there is a valid chain of conversions to ReturnType. If not, then we cannot rely on the default constructor because anything may happen inside.

> Username: barendgehrels  
> Created_at: 2017-01-29 22:05:11 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r98364720  

Yes, indeed I remember the workaround.  
However, we the construction (as it was) a lot in the whole Boost.Geometry code. It was suggested by Bruno, and the style (call the template argument as a constructor, even for value types which would otherwise be uninitialized) is made for templates (I see that I did write this before).  
  
It makes no sense to change it occasionally, in one location. If you want to change it, we should change it everywhere. But I'm not so sure about that.

> Username: awulkiew  
> Created_at: 2017-01-30 01:59:53 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r98372311  

Yes, we're not consistent across the library.  
  
We talked about a similar thing some time ago (also with @brunolalande) and AFAIK he also expressed that if you want to represent specific numeric value then you should rather assign it explicitly. See the discussion here: https://github.com/boostorg/geometry/pull/197#discussion-diff-23688564  
  
And I'd argue that currently the number of explicit initializations with 0 is greater than value initializations. Just try the following grep searches:  
  
`[identifier] = 0`  
`grep -rE '[_A-Z0-9a-z]+ \= 0' include/`  
Found in `algorithms, rtree, formulas, strategies, util`.  
Types `CT, calculation_type, robust_calcualtion_type, coordinate_type`, etc.  
  
`[identifier] = [identifier](0)`  
`grep -rE '[_A-Z0-9a-z]+ \= [_A-Z0-9a-z]+\(0\)' include/`  
Found in `algorithms, formulas, strategies, util`.  
Types `Distance, return_type, coordinate_type, CoordinateType, T`  
  
`[identifier] = [identifier]()`  
`grep -rE '[_A-Z0-9a-z]+ \= [_A-Z0-9a-z]+\(\)' include/`  
Found in `algorithms, strategies, util`  
Types `area_result_type, ReturnType, coordinate_type, distance_type`, etc.

> Username: barendgehrels  
> Created_at: 2017-02-01 18:58:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r98967508  

Thanks Adam for looking up the message of Bruno.   
And thanks for grepping.  
OK, I agree with 0 initialization.


---

## Review 31 [Commented]

> Username: barendgehrels  
> Created_at: 2017-01-28 19:59:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/359#pullrequestreview-18979991  

📁 include/boost/geometry/strategies/geographic/area_geographic.hpp

```diff
  25 |+ approximation that gives the ellipsoidal correction
  26 |+ 
  27 |+ }
```

> Username: barendgehrels  
> Created_at: 2017-01-28 19:59:35 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r98338193  

Detail but this curly brace is probably redundant


---

## Review 32 [Commented]

> Username: barendgehrels  
> Created_at: 2017-01-28 20:01:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/359#pullrequestreview-18980032  

📁 include/boost/geometry/strategies/geographic/area_geographic.hpp

```diff
 123 |+             {
 124 |+                 std::size_t times_crosses_prime_meridian
 125 |+                         = 1 + (m_crosses_prime_meridian / 2);
```

> Username: barendgehrels  
> Created_at: 2017-01-28 20:01:01 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r98338237  

does 1 and 2 also need a CT here?

> Username: vissarion  
> Created_at: 2017-01-31 15:49:30 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r98697985  

I think no since m_crosses_prime_meridian is of type std::size_t.


---

## Review 33 [Commented]

> Username: barendgehrels  
> Created_at: 2017-01-28 20:02:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/359#pullrequestreview-18980055  

📁 include/boost/geometry/strategies/spherical/area_huiller.hpp

```diff
 136 |+                 }
 137 |+ 
 138 |+                 std::cout << "(const=" << constant << ")";
```

> Username: barendgehrels  
> Created_at: 2017-01-28 20:02:33 UTC  
> Updated_at: 2017-02-01 18:15:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#discussion_r98338259  

Please remove unguarded std::cout  calls (and commented code, if redundant)


---

## Comment 34

> Username: barendgehrels  
> Created_at: 2017-01-28 20:04:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#issuecomment-275871117  

Sure, I am OK with merging and with the name area::geographic

---

## Comment 35

> Username: vissarion  
> Created_at: 2017-01-31 16:01:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#issuecomment-276405149  

Thanks for the comments! I addressed them and also  
-- remove strategy area_huiller since is currently redundant  
-- change to name area::spherical the currently sole spherical strategy for area  
-- keep the value initialization with 0 for now, but I do not have a strong opinion since there are inconsistencies in the library

---

## Comment 36

> Username: barendgehrels  
> Created_at: 2017-02-01 18:58:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/359#issuecomment-276747413  

Thanks for addressing the comments. I'm OK with merging.

---
