# #745 Support constexpr in points, segment, box, get, dot and cross products. [Merged]

> Username: awulkiew  
> Created at: 2020-08-14 22:43:39 UTC  
> Updated at: 2020-08-24 10:26:53 UTC  
> Merged at: 2020-08-24 10:26:53 UTC  
> Closed at: 2020-08-24 10:26:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/745  

I was playing with `constexpr`. This PR allows to do something like this:  
```  
using point = bg::model::point<double, 2, bg::cs::cartesian>;  
using point_xy = bg::model::d2::point_xy<double>;  
using point_xyz = bg::model::d3::point_xyz<double>;  
using box = bg::model::box<point>;  
using segment = bg::model::segment<point>;  
  
constexpr point p0 = point{};  
constexpr point p1 = point{ 1 };  
constexpr point_xy p2 = point_xy{ 1, 2 };  
constexpr point_xyz p3 = point_xyz{ 1, 2, 3 };  
  
constexpr double d0 = bg::get<0>(p0);  
constexpr double d1 = bg::get<0>(p1);  
constexpr double d2 = bg::get<1>(p2);  
constexpr double d3 = bg::get<2>(p3);  
  
constexpr double d12 = bg::dot_product(p1, p2);  
constexpr point_xyz p33 = bg::cross_product(p3, p3);  
      
constexpr box b = box{ p0, p1 };  
  
constexpr double d4 = bg::get<0, 0>(b);  
  
constexpr segment s = segment{ p0, p1 };  
  
constexpr double d5 = bg::get<0, 0>(s);  
```  
  
It is possible to call `constexpr cross_product` only if a Point is 3d and is constructible from 3 coordinates. This theoretically is a breaking change because in general case Point could define a ctor taking 3 values being something different than coordinates or having coordinates in a different order than xyz. So we could think about some other mechanism like additional trait for Points creation.  
  
It is possible to create `constexpr model::box` only if Point is convertible from passed argument and copy constructible.  
  
I kept the support for Points with no copy ctor in `model::box` but I don't think this is needed becasue we rely on copy ctors of points in the library in general so one of the `model::box` ctors could be removed. If not, then we should add tests for such points.  
  
Another step could be to support constexpr in point arithmetic operations or maybe even implement operators for points with enable_if, like it is done for vectors in QVM.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2020-08-19 07:55:53 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/745#pullrequestreview-470211960  

Cool, I like it. Thank you!

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2020-08-19 11:37:13 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/745#pullrequestreview-470371128  

LGTM

---

## Comment 3

> Username: awulkiew  
> Created_at: 2020-08-24 10:26:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/745#issuecomment-679044890  

Thanks for reviews, I'll address the backward compatibility issue of point construction in the next PR.

---
