# #1026 - is_valid() returns inconsistent result when used with cartesian<long double> strategy [Closed]

> Username: peter-popov  
> Created at: 2022-06-28 10:06:48 UTC  
> Updated at: 2022-07-06 14:02:24 UTC  
> Closed at: 2022-07-06 14:02:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/1026  

I store coordinates as `model::d2::point_xy<double>` but use `is_valid` with increased precision strategy `boost::geometry::strategies::cartesian<long double>`. I found this works better for some of the corner cases when two points in the loop are very close to each other.  
  
When validating the invalid polygons I observe the following inconsistency:  
```  
POLYGON((0 0, 1 1, 0.75 0.25, 0.750001 0.25, 0.75 0.250001, 0 0))  
Geometry has invalid self-intersections. A self-intersection point was found at (0.75, 0.250001); method: i; operations: u/i; segment IDs {source, multi, ring, segment}: {0, -1, -1, 1}/{0, -1, -1, 3}  
-----  
POLYGON((0 0, 1 1, 0.75 0.25, 0.7500001 0.25, 0.75 0.2500001, 0 0))  
Geometry has invalid self-intersections. A self-intersection point was found at (0.75, 0.25); method: i; operations: u/i; segment IDs {source, multi, ring, segment}: {0, -1, -1, 1}/{0, -1, -1, 3}  
-----  
POLYGON((0 0, 1 1, 0.75 0.25, 0.75000001 0.25, 0.75 0.25000001, 0 0))  
Valid  
-----  
POLYGON((0 0, 1 1, 0.75 0.25, 0.750000001 0.25, 0.75 0.250000001, 0 0))  
Valid  
-----  
POLYGON((0 0, 1 1, 0.75 0.25, 0.7500000001 0.25, 0.75 0.2500000001, 0 0))  
Geometry has spikes. A spike point was found with apex at (0.75, 0.25)  
-----  
POLYGON((0 0, 1 1, 0.75 0.25, 0.75000000001 0.25, 0.75 0.25000000001, 0 0))  
Geometry has spikes. A spike point was found with apex at (0.75, 0.25)  
```  
  
It's understandable that the error message can change from self-intersection to spike. But why for some coordinate values the geometry suddenly becomes valid?!  
  
This does not happen when I use the default strategy.  
  
Tested on:   
- Boost 1.78   
- Clang 13.0.0 x86_64-apple-darwin20.6.0  and gcc10

---

## Comment 1

> Username: vissarion  
> Created at: 2022-06-30 10:43:53 UTC  
> Updated at: 2022-06-30 10:44:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/1026#issuecomment-1171061365  

I cannot reproduce it in Boost `1.79` with `clang-11` on `20.04 Ubuntu x86_64`. That, is I am getting consistently non valid results for all geometries and all strategies.   
  
In particular, I am getting self-intersections for the first 4 polygons and spikes for the rest when the strategy is parametrized with `long double`. With less precision (strategy parametrized with `double`) I am getting the first 2 polygons with self-intersections and the rest with a spike. Clearly, there is no spike in the input so this is most probably an numerical issue.

---

## Comment 2

> Username: vissarion  
> Created at: 2022-06-30 14:14:40 UTC  
> Updated at: 2022-06-30 14:15:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/1026#issuecomment-1171275520  

Moreover, if instead of `boost::geometry::strategies::cartesian<long double>` strategy one uses   
```  
using CT = double;  
struct robust_cartesian : boost::geometry::strategies::cartesian<CT>  
    {  
        static auto side()  
        {  
            return boost::geometry::strategy::side::side_robust  
                <  
                    CT,  
                    boost::geometry::strategy::side::fp_equals_policy  
                >();  
        }  
    };  
```  
then there are no spikes computed both for `CT = double` and `CT = long double`.

---

## Comment 3

> Username: peter-popov  
> Created at: 2022-07-05 11:21:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/1026#issuecomment-1174945031  

Thanks, @vissarion. I can confirm that it works in 1.79. Unfortunately for 1.78, both approaches don't work. With the robust strategy result is more consistent because the last two polygons are also valid:  
```  
...  
POLYGON((0 0, 1 1, 0.75 0.25, 0.7500000001 0.25, 0.75 0.2500000001, 0 0))  
Valid  
-----  
POLYGON((0 0, 1 1, 0.75 0.25, 0.75000000001 0.25, 0.75 0.25000000001, 0 0))  
Valid  
```  
I will appreciate it if you know some workaround for 1.78. Otherwise, I will close this issue

---

## Comment 4

> Username: vissarion  
> Created at: 2022-07-05 18:45:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1026#issuecomment-1175379311  

Actually the main difference and probably one source of the issue is the "rescaling/robustness" which is disabled by default in 1.79. So one workaround would be to set ```#define BOOST_GEOMETRY_NO_ROBUSTNESS``` before you include the library. Then you will get something similar to 1.79.

---

## Comment 5

> Username: peter-popov  
> Created at: 2022-07-06 14:02:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/1026#issuecomment-1176261753  

Thank you for the support @vissarion. The `side_robust` strategy works as expected when I defined `BOOST_GEOMETRY_NO_ROBUSTNESS`.
