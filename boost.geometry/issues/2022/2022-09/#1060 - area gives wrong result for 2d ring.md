# #1060 - area gives wrong result for 2d ring? [Closed]

> Username: craffael  
> Created at: 2022-09-30 14:19:06 UTC  
> Updated at: 2022-09-30 19:51:41 UTC  
> Closed at: 2022-09-30 14:23:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/1060  

The following code calculates the area of a octagon:  
```  
#include <boost/geometry/geometry.hpp>  
#include <iostream>  
  
int main() {  
  using point_t = boost::geometry::model::d2::point_xy<double>;  
  boost::geometry::model::ring<point_t> ring;  
  
  boost::geometry::append(ring, point_t(0.3,0));  
  boost::geometry::append(ring, point_t(0.7,0));  
  boost::geometry::append(ring, point_t(1,0.3));  
  boost::geometry::append(ring, point_t(1,0.7));  
  boost::geometry::append(ring, point_t(0.7,1));  
  boost::geometry::append(ring, point_t(0.3,1));  
  boost::geometry::append(ring, point_t(0,0.7));  
  boost::geometry::append(ring, point_t(0,0.3));  
  
  std::cout << boost::geometry::area(ring) << std::endl;  
}  
```  
  
It outputs `-0.865` as area but I think this is wrong. According to my calculations, the area should be `-4*0.06-2*0.29=-0.82`:  
![image](https://user-images.githubusercontent.com/15138672/193290101-a44d7add-1695-4779-9a02-9299b0f3c538.png)

---

## Comment 1

> Username: craffael  
> Created at: 2022-09-30 14:23:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/1060#issuecomment-1263643932  

Ah I, forgot to "close the ring". I had to add the first point again as the last point and then it gave the expected result.
