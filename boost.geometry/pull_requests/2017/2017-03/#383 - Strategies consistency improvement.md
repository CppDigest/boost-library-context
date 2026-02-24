# #383 Strategies consistency improvement [Merged]

> Username: awulkiew  
> Created at: 2017-03-07 19:36:19 UTC  
> Updated at: 2017-09-06 12:27:04 UTC  
> Merged at: 2017-03-08 14:45:52 UTC  
> Closed at: 2017-03-08 14:45:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/383  

The changes done in this PR:  
- In every geographic strategy the same order of template parameters is used:  
  - `FormulaPolicy`,  
  - `SeriesOrder` (if required),  
  - `Spheroid`,  
  - `CalculationType`.  
- By default `SeriesOrder` is based on `FormulaPolicy`:  
  - andoyer -> 1  
  - thomas -> 2  
  - vincenty -> 4  
- `ExpandEpsN` and `LongSegment` template parameters are removed.  
- Strategies' constructors are made `explicit`  
- Intersection strategies are renamed:  
  - `relate_cartesian_segments` to `cartesian_segments`,  
  - `relate_geodesic_segments` to `geographic_segments`,  
  - `relate_spherical_segments` to `spherical_segments`,  
  - etc.  
- By default in newly added `area::spherical` strategy `radius = 1` is used for backward compatibility and consistency with spherical distance strategies.  
- `*.hpp` files are renamed in a way reducing redundancy and improving consistency, e.g.:  
  - `geographic/area_geographic.hpp` to `geographic/area.hpp`  
  - `cartesian/cart_intersect.hpp` to `cartesian/intersection.hpp`  
  - etc.  
- `template <...> InverseFormula` parameter is replaced with `typename FormulaPolicy` parameter, Formula parameters/policies that can be passed are added to `namespace strategy` in `strategies/geographic/parameters.hpp`:  
  - `strategy::andoyer`,  
  - `strategy::thomas`,  
  - `strategy::vincenty`.  
- `strategy::default_order<>` metafunction returning default `Order` from `FormulaPolicy` is added.

---

## Comment 1

> Username: vissarion  
> Created_at: 2017-03-08 14:03:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/383#issuecomment-285048461  

Thanks, I am ok with merging.

---

## Comment 2

> Username: vissarion  
> Created_at: 2017-03-08 14:54:02 UTC  
> Updated_at: 2017-03-08 14:55:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/383#issuecomment-285062053  

Just a comment for future notice. Regarding series order and inverse formulas there are two parameters:   
- the series order of the formula i.e. 1 for andoyer, 2 for thomas and not applicable to vincenty since it is an iterative algorithm  
- the series order used by the algorithms like area and intersection  
  
There is not direct connection between those two. The goal is to have a consistent choice for parameters regarding accuracy, e.g. if we use vincenty then it is reasonable to use some higher order series and the opposite for andoyer. Let's keep here the above defaults but we have to do some tests/experiments and possibly update the defaults.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2017-03-08 14:57:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/383#issuecomment-285063053  

Agreed.

---
