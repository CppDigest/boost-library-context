# #1116 - Regression in union of touching points including a touching interior ring [Closed]

> Username: barendgehrels  
> Created at: 2023-02-22 13:35:47 UTC  
> Updated at: 2023-02-24 14:30:07 UTC  
> Closed at: 2023-02-22 14:10:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/1116  

Thank you so much to fix #1108 and #1109. I have tested these cases and it works well.  
  
However, I've found a regression case.  
![image](https://user-images.githubusercontent.com/17040347/219736397-5f64d348-b352-4461-9ca3-acfb536c77c7.png)  
```  
void TestUnionInteger()  
{  
    namespace bg = boost::geometry;  
#if 1  
    using point = bg::model::point<int64_t, 2, bg::cs::cartesian>;  
#else  
    using point = bg::model::point<double, 2, bg::cs::cartesian>;  
#endif  
    using polygon = bg::model::polygon<point>;  
    using multi_polygon = bg::model::multi_polygon<polygon>;  
    polygon input0, input1;  
  
    input0.outer().assign({  
        {0,-25000},  
        {-50000,-25000},  
        {-50000,0},  
        {0,-25000}  
    });  
  
    input1.outer().assign({  
        {-100000,0},  
        {-100000,30000},  
        {-50000,0},  
        {-100000,0}  
    });  
  
    input1.inners().push_back({  
        {-50000,0},  
        {-75000,10000},  
        {-75000,5000},  
        {-50000,0}  
    });  
  
    assert(bg::is_valid(input0));  
    assert(bg::is_valid(input1));  
    multi_polygon output;  
    bg::union_(input0, input1, output);  
    assert(bg::is_valid(output));  
}  
```  
  
This case works well with 1.81.0 but fails with new code.  
  
_Originally posted by @Mitsuhiko-Matsukawa in https://github.com/boostorg/geometry/issues/1108#issuecomment-1435035130_

---

## Comment 1

> Username: barendgehrels  
> Created at: 2023-02-22 14:10:45 UTC  
> Updated at: 2023-02-22 14:11:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/1116#issuecomment-1440078375  

Right, I cannot reproduce it, but I realize that it's now fixed by addressing one of the review comments.  
So there was an overflow for integer coordinates. They are now handled in a different way, this problem is gone.  
(I verified it by disabling the fix and then I got your problem)  
Thanks again.  
I will create a testcase of this issue to assure correct behavior.  
  
If you have more cases, please report them, you find many issues, thanks a lot.

---

## Comment 2

> Username: Mitsuhiko-Matsukawa  
> Created at: 2023-02-24 14:30:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/1116#issuecomment-1443759365  

@barendgehrels   
I have applied your commit ec7f9c9887ad4b8a41f09b7d392c98aafdc34321 and this issue has been resolved.  
Thank you so much.
