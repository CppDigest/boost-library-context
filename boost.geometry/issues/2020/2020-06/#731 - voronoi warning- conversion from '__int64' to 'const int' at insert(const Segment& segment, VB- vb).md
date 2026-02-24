# #731 - voronoi warning: conversion from '__int64' to 'const int' at insert(const Segment& segment, VB* vb) [Closed]

> Username: yoavmil  
> Created at: 2020-06-22 09:04:05 UTC  
> Updated at: 2020-06-22 19:11:56 UTC  
> Closed at: 2020-06-22 13:38:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/731  

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

> Username: vissarion  
> Created at: 2020-06-22 09:59:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/731#issuecomment-647415569  

@yoavmil this issue is about `polygon` library so you should probably try to open an issue there https://github.com/boostorg/polygon/issues

---

## Comment 2

> Username: yoavmil  
> Created at: 2020-06-22 13:39:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/731#issuecomment-647525832  

moved to https://github.com/boostorg/polygon/issues/46
