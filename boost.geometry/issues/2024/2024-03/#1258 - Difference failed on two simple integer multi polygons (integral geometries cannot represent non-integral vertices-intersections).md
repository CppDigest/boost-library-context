# #1258 - Difference failed on two simple integer multi polygons (integral geometries cannot represent non-integral vertices/intersections) [Closed]

> Username: fililili  
> Created at: 2024-03-09 06:37:28 UTC  
> Updated at: 2024-03-28 02:36:06 UTC  
> Closed at: 2024-03-27 09:27:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/1258  

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
https://godbolt.org/z/dProo1bf7  
  
_Originally posted by @fililili in https://github.com/boostorg/geometry/discussions/1188_

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-03-23 09:56:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/1258#issuecomment-2016431844  

I can reproduce it, thanks for the report.

---

## Comment 2

> Username: vissarion  
> Created at: 2024-03-26 08:49:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/1258#issuecomment-2019848364  

I can also reproduce the issue, but I see a more general problem here. What result do we expect in such cases? Note that the output polygon cannot be represented with integral vertices. Thus there are some choices that we could consider:   
1. identify the situation and return some expection that the result cannot be represented  
2. return some approximation (e.g. the smallest polygon that contains the result, or the largest that is contained in the result)  
3. do not do anything and leave the responsibility to the user like in the cases where the input is invalid and there is not guarantee on the correctness of the output  
  
@barendgehrels is there some design choice for those cases that I might miss?

---

## Comment 3

> Username: vissarion  
> Created at: 2024-03-26 08:52:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/1258#issuecomment-2019852964  

As expected the user can create cases where the output is valid but incorrect.  
e.g. `POLYGON((0 0, 0 2, 5 1, 5 0, 0 0)), POLYGON((3 1, 1 2, 5 1, 3 1))`   
![Screenshot from 2024-03-26 10-38-10](https://github.com/boostorg/geometry/assets/3660366/dabbc9ef-8e86-419b-91ff-2bdb4a989eb2)

---

## Comment 4

> Username: vissarion  
> Created at: 2024-03-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/1258#issuecomment-2019855703  

Also if you change the output to have `double` coordinated while the input has `int` you will get the following compilation error:  
  
```boost/libs/geometry/issues_testing/issue_1258.cpp:24:49:   required from here  
boost/libs/geometry/issues_testing/../../../boost/range/value_type.hpp:26:12: error: no type named ‘type’ in ‘struct boost::range_iterator<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double> >, void>’  
   26 |     struct range_value : iterator_value< typename range_iterator<T>::type >  
      |            ^~~~~~~~~~~  
```

---

## Comment 5

> Username: barendgehrels  
> Created at: 2024-03-26 20:28:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/1258#issuecomment-2021405474  

@vissarion thanks for your input, I didn't visualize the case when I answered (not even mentally).  
Indeed, this is not a case which can be done right. We round intersection points (that is a recent change). But even then, the library is not designed for these kind of cases.  
We support integer because it avoids floating point problems, and it is therefore very useful for cases with large values (for example in the range 100000 - 1000000). Or Manhattan cases. But in these cases, it's just what comes out, and it can be invalid.  
  
I propose to close the issue.  
  
Maybe we can fix the case you (@vissarion ) tried, having output as double, to support these kind of cases. But that is not just  a bugfix, I think,

---

## Comment 6

> Username: vissarion  
> Created at: 2024-03-27 09:25:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/1258#issuecomment-2022296292  

OK I agree. I will close this issue for now.  
  
@fililili feel free to open it again if you have other issues or questions.

---

## Comment 7

> Username: fililili  
> Created at: 2024-03-28 02:28:47 UTC  
> Updated at: 2024-03-28 02:36:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/1258#issuecomment-2024288586  

Hi, @vissarion and @barendgehrels .  
Let me update some information I know.  
1. If we don't have a snap rounding algorithm, such problem will always happen no matter what range is. The lib clipper2 met same problem, too. https://angusj.com/clipper2/Docs/Robustness.htm  
2. The lib CGAL has mentioned some snap rounding algorithm for integer coordinate types. Snap rounding algorithm can avoid topo inconsistency and it can ensure snap be less than 1 unit.  
3. I have tried boolean algorithm with snap rounding based on this lib. https://github.com/fililili/best_clipper/blob/main/test.cpp. But, it's still not completed and cannot be used in practice.
