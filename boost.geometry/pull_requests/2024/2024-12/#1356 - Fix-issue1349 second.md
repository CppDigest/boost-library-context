# #1356 Fix/issue1349 second [Closed]

> Username: barendgehrels  
> Created at: 2024-12-15 15:12:56 UTC  
> Updated at: 2025-01-03 19:15:53 UTC  
> Closed at: 2024-12-28 10:25:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1356  

This is an updated version of #1350   
  
See also the pictures there.  
  
It should be build on top of #1355 In the past that was easy to configure, now I don't see it anymore.  
  
Summary of fixes:  
* #1349   
* #1221   
* #1350 (the comment made there) is kept valid (unlike using that PR)  
  
~There might be a follow-up, but that is far from ready so I think we should go first for this one.~  
I'm busy with the follow-up which is now in a promising state. Will close this one.

---

## Comment 1

> Username: tinko92  
> Created_at: 2024-12-20 09:33:57 UTC  
> Updated_at: 2024-12-20 10:22:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1356#issuecomment-2556618044  

I think this PR creates a regression for the symdiff of the following to geometries:  
  
geo1: MULTIPOLYGON(((14 10,14 8,12 8,12 6,14 6,14 2,12 2,12 0,6 0,6 2,4 2,4 0,0 0,0 2,2 2,2 4,0 4,0 10,2 10,2 14,6 14,6 12,8 12,8 14,10 14,10 12,12 12,12 10,14 10),(12 10,8 10,8 8,8 6,4 6,4 4,6 4,6 2,8 2,8 4,10 4,10 8,12 8,12 10),(4 6,4 8,2 8,2 6,4 6),(4 8,6 8,6 10,4 10,4 8)),((14 12,12 12,12 14,14 14,14 12)))  
geo2: MULTIPOLYGON(((6 2,6 0,0 0,0 2,2 2,2 4,4 4,4 2,6 2)),((10 2,10 0,8 0,8 2,10 2)),((14 4,14 2,12 2,12 4,14 4)))  
  
I will try to look at this in more detail later. I currently lack insight to review the algorithmic changes in detail (will try later). Until then, this example is based on random testing with the following code:  
  
https://gist.github.com/tinko92/fad450e1058ce8ceebde66a6b86d45c1  
  
The sizes in line 173 can be changed for larger/smaller examples. I usually have to leave it running for a while before it finds something. It finds cases for difference and union as well. I will try to make it reduce cases to minimal complexity later.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2024-12-20 18:45:16 UTC  
> Updated_at: 2024-12-21 09:33:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1356#issuecomment-2557539155  

> Until then, this example is based on random testing with the following code:  
  
Interesting test! Thanks for the link.  
  
So this is an alternative stress test for https://github.com/boostorg/geometry/blob/develop/test/robustness/overlay/areal_areal/recursive_polygons.cpp which I ran often (but it's now a while ago). All the test cases with names like `case_recursive_boxes_88` come from that test.  
  
I'm using your test now as a stochastic check for the various approaches. For example, the case you mentioned was already wrong on the `develop` branch if I'm correct. So this PR would be OK-ish with respect to that.  
  
A PR should make the behaviour in general better. But even if some cases in a stress test, which are not part of the existing test suite, might fail, it might still be acceptable.  
  
I'll make a table, which will help me.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2024-12-21 09:32:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1356#issuecomment-2558064172  

> I think this PR creates a regression for the symdiff of the following to geometries:  
  
By the way, the geo1 input was already invalid. A valid version is:  
```  
MULTIPOLYGON(((12 2,12 0,6 0,6 2,8 2,8 4,10 4,10 8,12 8,12 6,14 6,14 2,12 2)),((4 2,4 0,0 0,0 2,2 2,2 4,0 4,0 10,2 10,2 14,6 14,6 12,8 12,8 14,10 14,10 12,12 12,12 10,8 10,8 8,8 6,4 6,4 4,6 4,6 2,4 2),(4 8,2 8,2 6,4 6,4 8),(6 8,6 10,4 10,4 8,6 8)),((14 10,14 8,12 8,12 10,14 10)),((12 12,12 14,14 14,14 12,12 12)))  
```  
And then it looks completely OK to me.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2024-12-21 10:25:33 UTC  
> Updated_at: 2024-12-21 11:00:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1356#issuecomment-2558076317  

> I'll make a table, which will help me.  
  
So I used:  
* stress test @tinko92   
* recursive_polygons  
* the test suite (without failures)  
* the test suite (with failures)  
  
For 5 approaches:  
* current code: `develop`  
* approach from PR 1350   
* approach from PR 1356 , this PR  
* an unused third approach which looked nicer but it had regressions  
* the one I'm currently working on, using Boost.Graph `biconnected_components`  
  
| stress test                                          | Failures (in 250000 cases) |  
|-----------------------------------------------|----------------------------|  
| develop                                 | 11                         |  
| pr #1350                                | 7                          |  
| pr #1356                                | 5                          |  
| approach 3                              | 14145                      |  
| graph biconnected components      | 0                          |  
  
| recursive polygons                                      | Geometries (triangle) | Errors (triangle) | Errors (box) |  
|-----------------------------------------------|------------|--------|--------|  
| develop                                       | 635000     | 5      | 5 |  
| pr 1350                                      | 634932     | 7      | 1 |  
| pr 1356                                      | 634411     | 31     | 2 |  
| approach 3                                    | 634936     | 4      | 0|  
| graph biconnected components          | 633764     | 195    | 0|  
  
| test suite (union, intersection, difference, set_ops)           | Failures     | Failures with BOOST_GEOMETRY_TEST_FAILURES   |  
|-----------------------------------------------|----------------------------|----------|  
| develop                                       | 0                         |33                         |  
| pr 1350                      | 2          (in the new issue commented in that PR)               |32                         |  
| pr 1356                       | 0                         |30                         |  
| approach 3                      | 6   (in issue_869 , issue_1221 and issue_1349, and therefore it was a no-go)     | 36 |  
| graph biconnected components | 0          | 27 |

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2024-12-21 10:31:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1356#issuecomment-2558077577  

So, in summary, it depends how you look at it. No approach is perfect yet. Most improve what is in `develop`.  
  
The last one using `biconnected_components` is the most promising (for example, no errors in @tinko92 's stress test).  
  
But in the recursive polygon test, with triangles, it has several errors. I investigated two of them, and it would need a follow-up in other code (so: not in the isolation check). But for the rest it is conceptually the best solution.  
  
Questions:  
* can we depend on Boost.Graph ?  
* if not, there are various implementations of biconnected components, it is a well-known algorithm  
* will we first go for this PR and then later for the new approach (I can already publish it as a draft PR)  
* or will we also close this PR and wait?

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2024-12-21 10:44:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1356#issuecomment-2558080437  

@tinko92 will we add your test to the robustness tests?  
I like it, nice checking of the result using the bitset

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2024-12-28 10:25:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1356#issuecomment-2564290225  

Will be replaced

---

## Comment 8

> Username: tinko92  
> Created_at: 2025-01-03 15:33:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1356#issuecomment-2569410475  

> @tinko92 will we add your test to the robustness tests? I like it, nice checking of the result using the bitset  
  
@barendgehrels  Sorry, I forgot to respond to this, I will try to clean it up and prepare a PR.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2025-01-03 19:15:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1356#issuecomment-2569699292  

Cool, nice! I didn't write yet that I had to change that it should work for C++14...

---
