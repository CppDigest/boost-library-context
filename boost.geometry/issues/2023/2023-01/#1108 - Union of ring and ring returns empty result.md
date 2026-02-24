# #1108 - Union of ring and ring returns empty result. [Closed]

> Username: Mitsuhiko-Matsukawa  
> Created at: 2023-01-23 04:27:12 UTC  
> Updated at: 2023-02-22 13:36:47 UTC  
> Closed at: 2023-02-22 13:36:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/1108  

boost1.81.0  
Visual Studio 2019. x64 debug  
  
This is a regression from 1.80.0.  
If I revert a commit a908a21efeb773a88ec2c8999ba592dc161649e7, it works well.  
However, this commit is useful and it's not good to revert.  
We need to fix overlay, but I'm sorry I have no idea so far.  
  
If I change #if 1 to 0, it works well.  
  
![image](https://user-images.githubusercontent.com/17040347/213965367-4e5c2441-eb18-44f6-84c8-630ac683bf70.png)  
  
```  
void Test()  
{  
    namespace bg = boost::geometry;  
    using point = bg::model::point<double, 2, bg::cs::cartesian>;  
    using ring = bg::model::ring<point>;  
    using multi_polygon = bg::model::multi_polygon<bg::model::polygon<point>>;  
    ring input0, input1;  
  
    input0.assign({  
        {17,-2},  
        {18,-1.269679093926235014},  
        {12,0},  
        {22,0},  
        {17,-2},  
    });  
  
    input1.assign({  
        {22,1},  
        {22,0},  
        {14,0},  
#if 1  
        {18,-1.2696790939262529996},  
#else  
        {18,-1.269679093926235014},  
#endif  
        {12,0},  
        {22,1},  
    });  
  
    assert(bg::is_valid(input0));  
    assert(bg::is_valid(input1));  
    multi_polygon output;  
    bg::union_(input0, input1, output);  
    assert(!output.empty());  
}  
```

---

## Comment 1

> Username: barendgehrels  
> Created at: 2023-01-25 09:55:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1108#issuecomment-1403353153  

I can reproduce this as well, and indeed the get_cluster threshold is important here.  
Thanks for this information.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2023-01-25 14:52:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1108#issuecomment-1403742084  

I think we have to make the threshold in `get_cluster` higher. Then there is a cluster (obviously). This case is fixed then, and it breaks the cases you reported in #1081   
  
These case(s) should then be fixed in another way. As far as I can see, there is something wrong or missing in the turn information, from that one cluster (two turns) it should be able to find the next intersection point (which is: itself) and create the inner ring.  
  
Debug info I've now there is:  
![image](https://user-images.githubusercontent.com/334849/214595198-989332b2-4f2b-4082-a321-59faf7181a99.png)  
  
It's a bit messy, but the important info is in red: No next IP. That's wrong, it should find one. To be continued.

---

## Comment 3

> Username: Mitsuhiko-Matsukawa  
> Created at: 2023-01-30 04:10:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/1108#issuecomment-1407966725  

Thank you for chasing this issue.  
I agree that the turn information is something wrong and we need to investigate it.  
  
I understand if we make the cluster threshold higher, this case is resolved. But it just bypasses the turn issue.  
If we adjust the threshold higher, other cases may be wrong.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2023-02-01 10:55:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/1108#issuecomment-1411861508  

Indeed, we cannot change the cluster only, there is some other logic as well.  
I found at least one thing, which fixes this particular issue (with higher threshold) and keeps the cases of #1081 correct.  
To be continued, I hope I've more news or a PR today

---

## Comment 5

> Username: barendgehrels  
> Created at: 2023-02-01 15:36:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/1108#issuecomment-1412264418  

There are more specific checks necessary, or a more general approach. So I can't finish it today.  
  
More specificly, if you remove this check here : https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/traversal.hpp#L971  
  
Then the clustersize can be enlarged again (I use 100 now) without breaking #1081   
However, it breaks some tests from buffer. If I try to tweak that, I get other failures, so I'm not ready yet.

---

## Comment 6

> Username: barendgehrels  
> Created at: 2023-02-01 15:38:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/1108#issuecomment-1412266917  

I'm convinced now that that check should not be there. Because a union and intersection are similar (apart from travel direction), so there should not be code to handle it separately. We have it in a few places, therefore I mention: a more general approach.

---

## Comment 7

> Username: barendgehrels  
> Created at: 2023-02-08 19:05:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/1108#issuecomment-1423103290  

I looked at it closer today. I couldn't finish it for 100% but basically the solution is there and I'll create a PR soon (probably this weekend)

---

## Comment 8

> Username: barendgehrels  
> Created at: 2023-02-15 15:08:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/1108#issuecomment-1431519046  

Fixed by mentioned PR. All works now fine for me.  
Though we might in the future have a closer look at these clusters again, and how they are traversed. That's the hardest part.

---

## Comment 9

> Username: Mitsuhiko-Matsukawa  
> Created at: 2023-02-17 17:53:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1108#issuecomment-1435035130  

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

---

## Comment 10

> Username: barendgehrels  
> Created at: 2023-02-22 09:42:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/1108#issuecomment-1439710811  

Thanks @Mitsuhiko-Matsukawa , I will look to this situation as well.

---

## Comment 11

> Username: barendgehrels  
> Created at: 2023-02-22 13:36:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/1108#issuecomment-1440030591  

Closing this issue, after creating a new one of the regression
