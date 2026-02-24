# #1350 fix: revise traversal_switch_detector for isolated areas [Closed]

> Username: barendgehrels  
> Created at: 2024-12-04 17:20:19 UTC  
> Updated at: 2024-12-15 15:14:52 UTC  
> Closed at: 2024-12-15 15:14:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1350  

**Not ready for review**  
  
~Fixes: issue 1349~  
  
This is quite a revisal. Issue #1349 was caused by a logic error, and not by a precision error. It was easily fixed by removing the (in hindsight weird) condition (added by me) to reverse the meaning of isolated.  
  
But that caused the original issue again. I made an inverse case of that one as well, and that failed anyway (also with its earlier "fix") for difference.  
  
~I decided to go for another way to detect isolation, which is now clearer - but even then not that easy. Below some cases for reference.~  
  
Below some pictures of intersections (blue, green: input, turquoise: both (= intersection), dotted red/green: resulting intersection.  
  
Case 869 (only ii turns)  
![image](https://github.com/user-attachments/assets/f2ce1626-9dee-4b8b-a364-ada6b4b38af9)  
  
Case 128 (with a separate polygon connected as "multiple" with only ii turns (turn 6,9,18), it should not be isolated)  
  
![image](https://github.com/user-attachments/assets/0982eaef-95f0-4014-8a11-9ae953b3159a)  
  
Case 141 (idem but it should be isolated and the "multiple" region inside (turn 13,14) is a separate polygon)  
  
![image](https://github.com/user-attachments/assets/10615b85-763e-4973-9dd2-7b6c4588e918)  
  
 Case case_recursive_boxes_81 which looks the same, but here it are two outer polygons with a part (at turn 26, 27, 42)  
  
![image](https://github.com/user-attachments/assets/c867872c-0f13-4f45-885f-dfb7838f58f4)

---

## Comment 1

> Username: tinko92  
> Created_at: 2024-12-06 11:15:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1350#issuecomment-2522926066  

I think, this creates a regression. Consider  
  
```cpp  
#include <iostream>  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
  
namespace bg = boost::geometry;  
using point = bg::model::point<int, 2, bg::cs::cartesian>;  
using polygon = bg::model::polygon<point>;  
using multipoly = bg::model::multi_polygon<polygon>;  
  
int main() {  
    auto p1 = bg::from_wkt<multipoly>("MULTIPOLYGON(((2 10,2 8,0 8,0 10,2 10)),((10 8,10 2,8 2,8 0,0 0,0 4,2 4,2 8,4 8,4 10,6 10,6 8,6 6,8 6,8 8,10 8),(8 2,8 4,4 4,4 2,8 2)))");  
    auto p2 = bg::from_wkt<multipoly>("MULTIPOLYGON(((2 6,2 4,2 2,2 0,0 0,0 6,2 6)),((2 10,2 8,0 8,0 10,2 10)),((6 8,6 6,2 6,2 8,6 8)),((8 4,8 2,6 2,6 4,8 4)),((10 8,10 6,8 6,8 8,10 8)),((8 10,8 8,6 8,6 10,8 10)))");  
    multipoly p3;  
    bg::sym_difference(p1, p2, p3);  
    std::cout << bg::wkt(p3) << "\n";  
    return 0;  
}  
```  
  
Output on develop: MULTIPOLYGON(((10 6,10 2,8 2,8 0,2 0,2 4,0 4,0 6,10 6),(6 2,6 4,4 4,4 2,6 2)),((4 8,4 10,6 10,8 10,8 8,4 8)))  
Output on this branch: MULTIPOLYGON(((10 6,10 2,8 2,8 0,2 0,2 4,0 4,0 6,10 6)),((4 8,4 10,6 10,8 10,8 8,4 8)))

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2024-12-06 18:53:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1350#issuecomment-2523955522  

> I think, this creates a regression. Consider  
  
👍 I'll check

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2024-12-06 19:42:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1350#issuecomment-2524030760  

> > I think, this creates a regression. Consider  
>   
> 👍 I'll check  
  
Indeed it fails now. I have to revise it.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2024-12-07 23:41:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1350#issuecomment-2525345525  

The region information for #869 is incorrect - there is a fix that fixes this all but leads to yet another situation which goes wrong.  
I have to revise the code, probably getting rid of the isolation concept.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2024-12-11 12:36:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1350#issuecomment-2535877158  

@tinko92 we (@vissarion and I) added you as a collaborator, now I can add you as a reviewer, great.  
  
For the rest this PR is still in draft and might be closed by a replacement later this month.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2024-12-15 15:14:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1350#issuecomment-2543910752  

Replaced by #1356

---
