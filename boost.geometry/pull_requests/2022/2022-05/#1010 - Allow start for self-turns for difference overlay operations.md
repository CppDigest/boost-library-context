# #1010 Allow start for self-turns for difference overlay operations [Merged]

> Username: tinko92  
> Created at: 2022-05-29 23:23:26 UTC  
> Updated at: 2022-06-06 11:22:25 UTC  
> Merged at: 2022-06-06 07:24:37 UTC  
> Closed at: 2022-06-06 07:24:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1010  

This changes the handling of colocations in overlay for difference operations such that self-turns are not marked as not startable. As far as I can see, this fixes test case case_recursive_boxes_62 and, I believe, this also solves #900 without breaking any other cases.  
  
Here is a reduced version of that test case, transformed to integer coordinates, in case it needs further analysis:  
  
```cpp  
#include <iostream>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
  
namespace bg = boost::geometry;  
using point = bg::model::d2::point_xy<int>;  
using polygon = bg::model::polygon<point>;  
using multi_polygon = bg::model::multi_polygon<polygon>;  
  
int main()  
{  
    auto a = bg::from_wkt<multi_polygon>("MULTIPOLYGON(((5 3,6 4,5 2,4 4,5 3)),((4 4,4 6,6 4,4 4)),((6 4,6 5,8 4,6 4)),((4 4,2 4,2 5,4 4)))");  
    auto b = bg::from_wkt<multi_polygon>("MULTIPOLYGON(((1 1,1 6,9 6,9 1,1 1)))");  
    multi_polygon diff;  
    bg::difference(b, a, diff);  
    std::cout << bg::to_wkt(diff);  
    return 0;  
}  
```  
  
I am not certain that I completely understand the overlay algorithm, though. Is it based on a specific polygon overlay algorithm that can be found in the literature?  
  
With this change applied, I no longer find any error in the set operation results in Boost.Geometry compared to CGAL for any of the test cases, see https://tinko92.github.io/bg_cgal_set_operations_comparison/ (**Edit**: The results on the page are based on the branch in this PR and sorting the column "(BG Real Δ CGAL Real area) / (CGAL Real area)" in descending order shows that there are no examples in which BG and CGAL disagree if both use an infinite-precision number type.) and https://github.com/tinko92/boost-geometry-cgal-set-operation-comparison for the full list and the code used to do the comparison. Maybe the remaining issues (with BOOST_GEOMETRY_TEST_FAILURES)  can be solved with suitable strategies.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2022-06-01 10:26:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1010#issuecomment-1143420042  

Hi @tinko92 , thanks for the PR!  
I can confirm that it works, fixes the additional case. Cool list with comparisons!

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2022-06-01 10:29:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1010#issuecomment-1143423825  

Can you add the fixed testcase to the unit testsuite? I checked if (but it's more convenient in this PR):  
  
In file `multi_overlay_cases.hpp`: after `issue_888_53` add :  
```  
static std::string issue_900[2] =  
{  
    "MULTIPOLYGON(((5 3,6 4,5 2,4 4,5 3)),((4 4,4 6,6 4,4 4)),((6 4,6 5,8 4,6 4)),((4 4,2 4,2 5,4 4)))",  
    "MULTIPOLYGON(((1 1,1 6,9 6,9 1,1 1)))"  
};  
  
```  
  
And in difference_multi.cpp, after the same case, add  
```  
TEST_DIFFERENCE(issue_900, 0, 0.0, 2, 35, 2);  
```  
  
Thanks!

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2022-06-01 10:51:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1010#issuecomment-1143444829  

The algorithm is Graph Traversal and is based on Weiler-Atherton and Greiner–Hormann  (so *not* on Vatti).  
  
So the basic algorithm follows exactly that graph traversal. But things as colocated intersections make it more difficult, those implementations are basically added by myself.  
  
See for example https://www.sciencedirect.com/science/article/pii/S259014861930007X (but I didn't use that article, it didn't exist yet).

---

## Review 4 [Approved]

> Username: barendgehrels  
> Created_at: 2022-06-01 10:56:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1010#pullrequestreview-991774320  

📁 include/boost/geometry/algorithms/detail/overlay/handle_colocations.hpp

```diff
 519 |                     && is_self_turn<OverlayType>(turn))
 520 |             {
 521 |                 // Difference needs the self-turns, TODO: investigate
```

> Username: barendgehrels  
> Created_at: 2022-06-01 10:56:02 UTC  
> Updated_at: 2022-06-01 10:56:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1010#discussion_r886668605  

The comment should probably be changed as well. But it is vague anyway and probably was already not up-to-date anymore.  
  
Current behavior is that, for self turns for differences, startable is not set to false. But the code says that already, you can also omit it, or keep the `TODO`


---

## Comment 5

> Username: tinko92  
> Created_at: 2022-06-02 14:21:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1010#issuecomment-1144924817  

Thanks for the link and the feedback. I hope I didn't forget anything in the updated commit. With regard to the comment, out of your suggestions I chose the one of keeping the TODO.

---

## Review 6 [Approved]

> Username: vissarion  
> Created_at: 2022-06-02 14:49:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1010#pullrequestreview-993672029  

Looks good! Thanks.  
  
ps. was that a typo?

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2022-06-06 07:24:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/1010#issuecomment-1147134704  

> Thanks for the link and the feedback. I hope I didn't forget anything in the updated commit. With regard to the comment, out of your suggestions I chose the one of keeping the TODO.  
  
Looks good, thanks for the testcase, I'll merge it.

---
