# #46 - voronoi warning: conversion from '__int64' to 'const int' at insert(const Segment& segment, VB* vb) [Open]

> Username: yoavmil  
> Created at: 2020-06-22 13:38:15 UTC  
> Updated at: 2020-06-22 15:39:26 UTC  
> Url: https://github.com/boostorg/polygon/issues/46  

My datatype is int64, and for some reason Boost is converting it to int.  
` struct Point { int64 X, Y; } `  
  
> polygon\voronoi.hpp(68,8): warning C4244: 'argument': conversion from '__int64' to 'const int', possible loss of data  
  
```c++  
template <typename Segment, typename VB>  
typename enable_if<  
  typename gtl_if<  
    typename is_segment_concept<  
      typename geometry_concept<Segment>::type  
    >::type  
  >::type,  
  std::size_t  
>::type insert(const Segment& segment, VB* vb) {  
  return vb->insert_segment(  
      x(low(segment)), y(low(segment)), // this is where the warning comes from  
      x(high(segment)), y(high(segment)));  
}  
```  
  
Maybe I'm doing something wrong, please advise, and maybe it is similar to [this](https://github.com/boostorg/geometry/issues/629).

---

## Comment 1

> Username: bubnikv  
> Created at: 2020-06-22 14:40:04 UTC  
> Url: https://github.com/boostorg/polygon/issues/46#issuecomment-647561281  

By default the vertices are int32. If you want the full scale of 64bit int, then you have to provide your own traits  
  
https://www.boost.org/doc/libs/1_72_0/libs/polygon/doc/voronoi_advanced_tutorial.htm

---

## Comment 2

> Username: yoavmil  
> Created at: 2020-06-22 15:39:26 UTC  
> Url: https://github.com/boostorg/polygon/issues/46#issuecomment-647603277  

Actually I am using traits and I'm following the tutorial. Please take a glance at my code.  
  
```c++  
struct Point { int64 X, Y; }  
  
namespace boost {  
namespace polygon {  
  
template <>  
struct geometry_concept<Point> {  
	typedef point_concept type;  
};  
  
template <>  
struct point_traits<Point> {  
	typedef int64 coordinate_type;  
  
	static inline coordinate_type get(  
		const Point& point, orientation_2d orient) {  
		return (orient == HORIZONTAL) ? point.X : point.Y;  
	}  
};  
  
template <>  
struct geometry_concept<BoostSegment> {  
	typedef segment_concept type;  
};  
  
template <>  
struct segment_traits<BoostSegment> {  
	typedef Point point_type;  
  
	static inline point_type get(const BoostSegment& segment, direction_1d dir) {  
		return dir.to_int() ? segment.first : segment.second;  
	}  
};  
  
}  // polygon  
}  // boost  
```
