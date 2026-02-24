# #1378 - for_each_point missing specialization for Box concept [Open]

> Username: ernestum  
> Created at: 2025-03-09 08:21:28 UTC  
> Updated at: 2025-03-09 08:25:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1378  

Hi,  
  
thanks for this great library. I enjoy using it and learn a lot about templates along the way!  
  
I noticed, that [for_each_point](https://www.boost.org/doc/libs/1_87_0/libs/geometry/doc/html/geometry/reference/algorithms/for_each/for_each_point.html) function does not work on my Box geometry.  
Probably because `algorithms/for_each.hpp` is missing a   
  
```c++  
template <Box Point>  
struct for_each_point<Box, box_tag>  
    : detail::for_each::fe_point_box  
{};  
````  
  
Tested with boost version 1.87.0  
  
Have a nice day!
