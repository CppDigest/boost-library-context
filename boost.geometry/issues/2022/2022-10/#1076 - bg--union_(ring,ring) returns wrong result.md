# #1076 - bg::union_(ring,ring) returns wrong result [Closed]

> Username: Mitsuhiko-Matsukawa  
> Created at: 2022-10-21 15:42:57 UTC  
> Updated at: 2022-11-16 13:00:45 UTC  
> Closed at: 2022-11-16 13:00:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/1076  

boost1.80, Visual Studio 2017  
  
```  
void Test()  
{  
    namespace bg = boost::geometry;  
    using point = bg::model::point<double, 2, bg::cs::cartesian>;  
  
#if 0  
    //correct result  
    bg::model::ring<point> ring0 = {  
        { 981,98 },  
        { 927,0 },  
        { 970,98 },  
        { 981,98 }  
    };  
  
    bg::model::ring<point> ring1 = {  
        { 927,0 },  
        { 981,98 },  
        { 995,98 },  
        { 927,0 }  
    };  
#else  
    //wrong result  
    bg::model::ring<point> ring0 = {  
        { 981.792858339935151,98 },  
        { 927.152135631899114,0 },  
        { 970,98 },  
        { 981.792858339935151,98 }  
    };  
  
    bg::model::ring<point> ring1 = {  
        { 927.152135631899114,0 },  
        { 981.792858339935151,98 },  
        { 995,98 },  
        { 927.152135631899114,0 }  
    };  
#endif  
  
    bg::model::multi_polygon<bg::model::polygon<point>> result;  
    bg::union_(ring0, ring1, result);  
  
    auto area0 = bg::area(ring0);  
    auto area1 = bg::area(ring1);  
    auto area_result = bg::area(result);  
    assert(std::max(area0, area1) < area_result );  
}  
```

---

## Comment 1

> Username: Mitsuhiko-Matsukawa  
> Created at: 2022-10-21 15:57:05 UTC  
> Updated at: 2022-10-27 03:06:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/1076#issuecomment-1287154471  

If I input the above data, [get_distance_measure](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/get_distance_measure.hpp#L97-L109) is called at these parameters.  
```  
p1=(927.15213563189911, 0.0000000000000000);  
p2=(981.79285833993515, 98.000000000000000);  
p=(981.79285833993515, 98.000000000000000);  
```  
If p is equal to p1 or p2, the distance should be 0. However, it returns 1.4551915228366852e-11.  
If I fix this function and return 0, the result will be correct.  
  
https://github.com/boostorg/geometry/commit/c1b8dbbc75d83fff00e7198c41836d18df4ce227

---

## Comment 2

> Username: Mitsuhiko-Matsukawa  
> Created at: 2022-10-21 16:12:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/1076#issuecomment-1287171346  

It might be better to fix [handle_imperfect_touch ](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp#L615).  
We need a tolerance.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2022-11-09 10:10:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/1076#issuecomment-1308516600  

Thanks for the report. I can reproduce it and I am considering your solution.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2022-11-09 12:07:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/1076#issuecomment-1308653325  

It is fixed in a similar way as your fix, but slightly different to avoid regressions elsewhere.  
Thanks again for the report and the analysis.
