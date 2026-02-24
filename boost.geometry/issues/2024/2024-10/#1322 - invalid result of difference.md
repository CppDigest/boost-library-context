# #1322 - invalid result of difference [Closed]

> Username: barendgehrels  
> Created at: 2024-10-02 14:58:12 UTC  
> Updated at: 2024-11-16 10:08:25 UTC  
> Closed at: 2024-11-16 10:06:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/1322  

### Discussed in https://github.com/boostorg/geometry/discussions/1188  
  
<div type='discussions-op-text'>  
  
<sup>Originally posted by **fililili** August 21, 2023</sup>  
```cpp  
#include <iostream>  
#include <vector>  
#include <cassert>  
  
#include <boost/geometry.hpp>  
  
int main()  
{  
    using polygon = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<int> >;  
    using multi_polygon = boost::geometry::model::multi_polygon<polygon>;  
  
    multi_polygon one, two;  
  
    boost::geometry::read_wkt(  
        "MULTIPOLYGON(((0 0, 0 2, 5 1, 5 0, 0 0)))", one);  
    assert(boost::geometry::is_valid(one));  
  
    boost::geometry::read_wkt(  
        "MULTIPOLYGON(((3 1, 1 2, 5 1, 3 1)), ((0 0, 0 2, 5 0, 0 0)))", two);  
    assert(boost::geometry::is_valid(two));  
  
    multi_polygon output;  
    boost::geometry::difference(one, two, output);  
    assert(boost::geometry::is_valid(output));  
  
    std::cout << "result:" << std::endl;  
    std::cout << boost::geometry::wkt(output) << std::endl;  
      
    return 0;  
}  
```  
Here is an exmple, `assert(boost::geometry::is_valid(output));` will failed.  
Do we have a well designed snap rouding algorithm? I can find some things in CGAL (https://doc.cgal.org/latest/Snap_rounding_2/index.html).  
https://godbolt.org/z/dProo1bf7</div>

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-11-16 10:06:58 UTC  
> Updated at: 2024-11-16 10:08:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/1322#issuecomment-2480501885  

It's a trivial case, using `float` and `double` all is fine.  
When using `int` on a grid of 5 by 5 the intersection points will be rounded to the grid, obviously.  
That might cause invalidities indeed because, when rounding, future intersections which would be apparent in a next step are not taken into account.  
  
The `int` is meant to avoid floating point precision. This works well on a grid of, for example, `100000` by `100000`. And it might work on smaller grids, but rounding errors might cause misbehavior. There are no plans to fix that.  
  
Closing as `wont fix`  
  
Below your case where all coordinates are multiplied by 10. Then there is obviously no error.   
  
![Image](https://github.com/user-attachments/assets/9928be94-012c-4e2e-9c42-0bbbb5dbddf1)  
  
And then the original case, where intersection point 2 moves to the south because of rounding. That results in a self intersection, which is indeed invalid.  
  
  
![Image](https://github.com/user-attachments/assets/6e2fef9c-3bc5-4cbb-ab8a-bd9810836d63)
