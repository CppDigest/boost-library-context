# #1285 - Type/Value mismatch in the point_xy.hpp file [Closed]

> Username: giapvn  
> Created at: 2024-05-21 08:43:36 UTC  
> Updated at: 2024-09-30 18:15:55 UTC  
> Closed at: 2024-09-30 18:15:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1285  

I am working with libboost (1.74) on Jetson AGX Orin now.  
- Before, I worked on Jetpack 5.x (CUDA 11.4, GCC 9.4.0) without any error.  
- After, I upgraded Jetpack to 6.0 GA (CUDA 12.3, GCC 11.4) and worked with same project. However, some error appeared as below:  
```  
/usr/include/boost/geometry/geometries/point_xy.hpp: In member function ‘void boost::geometry::model::d2::point_xy<CoordinateType, CoordinateSystem>::x(const CoordinateType&)’:  
/usr/include/boost/geometry/geometries/point_xy.hpp:74:27: error: type/value mismatch at argument 1 in template parameter list for ‘template<class _Key, class _Compare, class _Alloc> class std::set’  
   74 |     { this->template set<0>(v); }  
      |                           ^  
/usr/include/boost/geometry/geometries/point_xy.hpp:74:27: note:   expected a type, got ‘0’  
/usr/include/boost/geometry/geometries/point_xy.hpp:74:27: error: template argument 2 is invalid  
/usr/include/boost/geometry/geometries/point_xy.hpp:74:27: error: template argument 3 is invalid  
/usr/include/boost/geometry/geometries/point_xy.hpp: In member function ‘void boost::geometry::model::d2::point_xy<CoordinateType, CoordinateSystem>::y(const CoordinateType&)’:  
/usr/include/boost/geometry/geometries/point_xy.hpp:78:27: error: type/value mismatch at argument 1 in template parameter list for ‘template<class _Key, class _Compare, class _Alloc> class std::set’  
   78 |     { this->template set<1>(v); }  
      |                           ^  
/usr/include/boost/geometry/geometries/point_xy.hpp:78:27: note:   expected a type, got ‘1’  
/usr/include/boost/geometry/geometries/point_xy.hpp:78:27: error: template argument 2 is invalid  
/usr/include/boost/geometry/geometries/point_xy.hpp:78:27: error: template argument 3 is invalid  
In file included from /usr/include/boost/math/tools/cxx03_warn.hpp:9,  
                 from /usr/include/boost/math/constants/constants.hpp:11,  
                 from /usr/include/boost/geometry/util/math.hpp:29,  
                 from /usr/include/boost/geometry/core/radian_access.hpp:33,  
                 from /usr/include/boost/geometry/geometry.hpp:51,  
                 from /usr/include/boost/geometry.hpp:17,  
```  
In order to fix this issue, I tried to upgrade libboost version to 1.81 and 1.83. At the same time, I also upgraded GCC version to 12.3 (and 13.2 but it is not supported by CUDA complier and GNU). But the issue is still not resolved.  
![Screenshot from 2024-05-21 08-52-11](https://github.com/boostorg/geometry/assets/20217874/d26f18cc-f657-4587-9191-7960f7d195c8)  
Please help me to debug this problem.

---

## Comment 1

> Username: vissarion  
> Created at: 2024-05-21 09:03:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/1285#issuecomment-2122134021  

Could you please give a minimal example of code where this error appears?

---

## Comment 2

> Username: giapvn  
> Created at: 2024-05-21 09:28:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/1285#issuecomment-2122184672  

As in the error message, the error appeared right where I include library `#include <boost/geometry.hpp>` without implementing any code yet.

---

## Comment 3

> Username: vissarion  
> Created at: 2024-05-21 11:25:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/1285#issuecomment-2122415988  

OK, thanks. I need more detailed instructions on how to reproduce the error. Since I am not familiar with Jetson, could you please provide more details, e.g. what is the Linux  distribution used (Jetson, Ubuntu etc.).   
  
Also did you get the same error with JetPack 6.0 (production release)?  
  
Did you try with the latest boost release i.e. 1.85?

---

## Comment 4

> Username: barendgehrels  
> Created at: 2024-09-30 18:15:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1285#issuecomment-2383868552  

No response, closing
