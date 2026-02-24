# #852 - intersection produces invalid polygon (is_valid fails) with (nearly) duplicated point [Open]

> Username: rgreenblatt  
> Created at: 2021-05-24 04:01:31 UTC  
> Updated at: 2022-05-29 20:49:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/852  

```cpp  
#include <boost/geometry.hpp>  
  
#include <iostream>  
  
int main() {  
  using Point = boost::geometry::model::d2::point_xy<double>;  
  using Polygon = boost::geometry::model::polygon<Point>;  
  
  Polygon l{{  
      {0.33333327000590729, 0.33333344706803125},  
      {0.50967067548140821, 0},  
      {0, 0},  
      {0, 0.93199284523078707},  
      {0.33333327000590729, 0.33333344706803125},  
  }};  
  Polygon r{{  
      {0, 0.8554426406197928},  
      {0, 1},  
      {1, 0},  
      {0.54614530500375813, 0},  
      {0, 0.8554426406197928},  
  }};  
  assert(boost::geometry::is_valid(l));  
  assert(boost::geometry::is_valid(r));  
  std::vector<Polygon> out;  
  boost::geometry::intersection(l, r, out);  
  for (const Polygon &p : out) {  
    std::cout << boost::geometry::wkt(p) << std::endl;  
    assert(boost::geometry::is_valid(p)); // fails!  
  }  
}  
```  
Fails with:  
```  
POLYGON((0.333333 0.333333,0 0.855443,0 0.931993,0.333333 0.333333,0.333333 0.333333))  
a.out: temp.cpp:29: int main(): Assertion `boost::geometry::is_valid(p)' failed.  
[1]    2691824 abort (core dumped)  ./a.out  
```  
This reproduces with 1.75 and the develop branch of this repo.  
Note that the 2 points which are very close to (1/3, 1/3) as output by wkt are not equal - they are just very close.

---

## Comment 1

> Username: tinko92  
> Created at: 2022-05-29 20:49:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/852#issuecomment-1140522083  

I can no longer reproduce this with 1.79 (still core dumps with 1.78), so maybe it is fixed.
