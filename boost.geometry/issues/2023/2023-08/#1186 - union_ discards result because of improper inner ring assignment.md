# #1186 - union_ discards result because of improper inner ring assignment [Closed]

> Username: AndreMeyerRMC  
> Created at: 2023-08-18 10:31:02 UTC  
> Updated at: 2023-09-13 18:53:47 UTC  
> Closed at: 2023-09-13 18:53:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/1186  

In the following union_ - operation, result is empty even though both input shapes are valid. This seems to stem from an improper assignment of an inner ring. In the add_rings<GeometryOut> function, the first inner ring of the SECOND poly in polyB gets assigned to the first output poly, which ist the FIRST ring of polyB and as such completely outside of the inner ring.  
  
Tested in with msvc 14.1 boost 1.82.0 and 1.83.0 and godbolt clang 16 boost 1.82.0  
![boost_union2](https://github.com/boostorg/geometry/assets/86659038/571fdbff-cb05-4b02-b6f1-6b8b315e1bfa)  
  
```  
#include <iostream>  
  
#include <boost/geometry.hpp>  
  
using boost_coordinate_t = double;  
using boost_point_2d = boost::geometry::model::d2::point_xy<boost_coordinate_t>;  
using boost_polygon_2d = boost::geometry::model::polygon<boost_point_2d>;  
using boost_multipolygon_2d = boost::geometry::model::multi_polygon<boost_polygon_2d>;  
  
int main()  
{  
    auto polyA = boost::geometry::from_wkt<boost_polygon_2d>("POLYGON((\  
-13848.144652755556 6710443.1496919869,\  
-13847.699374792446 6710443.1496919869,\  
-13847.810694283222 6710440.1096301023,\  
-13848.255972246334 6710440.2884572418,\  
-13848.144652755556 6710443.1496919869))");  
  
    auto polyB = boost::geometry::from_wkt<boost_multipolygon_2d>("MULTIPOLYGON(((\  
-13848.144652755556 6710443.1496919869,\  
-13848.144652755556 6710443.3285191935,\  
-13847.699374792446 6710443.3285191935,\  
-13848.144652755556 6710443.1496919869\  
)),((\  
-13848.144652755556 6710443.1496919869,\  
-13848.255972246334 6710440.2884572418,\  
-13847.810694283222 6710440.1096301023,\  
-13847.699374792446 6710443.1496919869,\  
-13847.365416320112 6710442.9708647905,\  
-13846.029582430780 6710442.9708647905,\  
-13846.029582430780 6710443.3285191935,\  
-13846.029582430780 6710443.6861736169,\  
-13845.918262940002 6710444.0438280506,\  
-13845.918262940002 6710444.5803097384,\  
-13845.918262940002 6710444.7591369776,\  
-13845.918262940002 6710445.1167914625,\  
-13845.472984976890 6710444.5803097384,\  
-13845.584304467668 6710444.2226552768,\  
-13845.584304467668 6710442.9708647905,\  
-13845.250345995333 6710442.9708647905,\  
-13845.250345995333 6710443.1496919869,\  
-13845.027707013778 6710443.3285191935,\  
-13844.916387523001 6710443.1496919869,\  
-13844.805068032223 6710442.9708647905,\  
-13844.471109559890 6710442.9708647905,\  
-13844.359790069113 6710442.7920375923,\  
-13844.359790069113 6710442.4343832079,\  
-13844.471109559890 6710439.0366673544,\  
-13844.693748541444 6710435.8177799582,\  
-13844.693748541444 6710434.9236447969,\  
-13846.474860393890 6710435.1024718173,\  
-13847.810694283222 6710435.1024718173,\  
-13848.144652755556 6710435.1024718173,\  
-13851.372917988112 6710435.2812988469,\  
-13851.595556969667 6710435.2812988469,\  
-13851.595556969667 6710435.4601258812,\  
-13851.372917988112 6710438.6790131358,\  
-13851.372917988112 6710439.9308029665,\  
-13852.152154423557 6710440.1096301023,\  
-13852.152154423557 6710440.4672843833,\  
-13851.929515442002 6710443.5073464029,\  
-13849.814445117223 6710443.5073464029,\  
-13849.814445117223 6710443.1496919869,\  
-13848.144652755556 6710443.1496919869\  
),(\  
-13850.037084098778 6710440.2884572418,\  
-13851.372917988112 6710440.2884572418,\  
-13851.372917988112 6710439.9308029665,\  
-13850.037084098778 6710439.9308029665,\  
-13850.037084098778 6710440.2884572418\  
),(\  
-13847.476735810891 6710440.1096301023,\  
-13847.810694283222 6710440.1096301023,\  
-13847.922013773999 6710439.9308029665,\  
-13847.588055301669 6710439.7519758362,\  
-13847.476735810891 6710440.1096301023\  
)))");  
  
    boost_multipolygon_2d result;  
    boost::geometry::union_(polyA, polyB, result);  
  
    std::cout << "A: " << boost::geometry::num_points(polyA)  
     << "\nB: " << boost::geometry::num_points(polyB)  
     << "\nR: " << boost::geometry::num_points(result);  
  
    return 0;  
}  
```

---

## Comment 1

> Username: barendgehrels  
> Created at: 2023-08-23 09:30:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1186#issuecomment-1689614811  

:+1: thanks, for the source code and the most possible root cause. I can reproduce it

---

## Comment 2

> Username: barendgehrels  
> Created at: 2023-08-23 13:29:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/1186#issuecomment-1689969695  

I've a fix, will create a PR later today. It's not the ring assignment itself, it is because a turn is not set as startable, and that comes from a tolerance value which is too high.   
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp#L328  
will be set to `100`, the current value is excessive (changing it causes 2 regressions in buffer, but they are with rescaling, so the new value should be right, it's then the same as https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/get_clusters.hpp#L43)

---

## Comment 3

> Username: barendgehrels  
> Created at: 2023-08-23 13:31:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/1186#issuecomment-1689972444  

I will also check if it fixes #1183 too

---

## Comment 4

> Username: barendgehrels  
> Created at: 2023-08-23 16:36:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/1186#issuecomment-1690282905  

PR is created: #1189   
It fixes this issue. Unit test (for a simpler version of these geometries, with the same problem) is added.  
It does not fix #1183

---

## Comment 5

> Username: barendgehrels  
> Created at: 2023-09-13 18:53:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/1186#issuecomment-1718152894  

Fixed
