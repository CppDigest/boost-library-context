# #1434 - Missing, and recursive, graph dependency. [Closed]

> Username: grafikrobot  
> Created at: 2025-10-08 13:14:35 UTC  
> Updated at: 2025-12-01 07:25:51 UTC  
> Closed at: 2025-10-26 19:17:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434  

This commit (https://github.com/boostorg/geometry/commit/805ff654e869030c4ba70f37fad6c3825f59c077) added a dependency to Boost.Graph to the CML. But it did not make the equivalent change to `build.jam`. If that had been done, it would have become clear (because of build errors) that the new dependency creates a dependency cycle.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2025-10-09 17:56:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434#issuecomment-3386950725  

I'm curious, what is the new dependency cycle?

---

## Comment 2

> Username: grafikrobot  
> Created at: 2025-10-09 18:03:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434#issuecomment-3386974363  

https://pdimov.github.io/boostdep-report/develop/graph.html#geometry

---

## Comment 3

> Username: grafikrobot  
> Created at: 2025-10-09 18:04:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434#issuecomment-3386977026  

https://pdimov.github.io/boostdep-report/develop/geometry.html#graph

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2025-10-09 23:43:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434#issuecomment-3387799272  

Hi @barendgehrels. I recently added a dependency on Boost.Geometry to Boost.Graph to replace the custom geometry code we had in a planar graph algorithm (which to your credit fixed the problems in it straight away), and I remember thinking "Maybe I should let them know that now we depend on them..." but here are.  :)

---

## Comment 5

> Username: jeremy-murphy  
> Created at: 2025-10-10 00:20:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434#issuecomment-3387853161  

At the moment it's only a single algorithm where we use some predicates, so although we have a recursive dependency at the library level it's (obviously) not recursive at the headers. So, although it's not ideal, do we actually need to do anything to fix it?  
Off the cuff, it would require splitting the parts of each library that depend on the other out into new libraries that depend on both. I mean, like new library Boost.Graph.Planar would depend on Boost.Graph and Boost.Geometry, and ... oh, I just realized that you've actually got a whole new `graph` directory of code. I don't have time to look through it all right now, but is it specific to geometry or can we generalize it and put it in Boost.Graph? Anyway, I'm happy to work together to find a great solution.

---

## Comment 6

> Username: barendgehrels  
> Created at: 2025-10-23 17:54:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434#issuecomment-3438370476  

hi @jeremy-murphy , sorry for my delayed reaction.  
  
By big chance - so you added a dependency on Boost.Geometry in Dec 2024 (indeed I didn't notice) and in Jan 2025 I started revising our graph traversal using a real graph ;-) My efforts were merged in or around April 2024.  
  
> do we actually need to do anything to fix it?  
Ideally yes, probably. We mainly use the algorithm `biconnected_components`.   
But of course also the graph type `boost::adjacency_list` and the functions to fill it, and traverse through it.  
  
Within our team we discussed if we should another dependency, for using one algorithm only. But because it's a real graph algorithm, we thought we should, and not implement it ourselves again.  
  
And it turned out quite well, greatly simplifying the algorithm and fixing many existing problems (though later a few others came back, still to be investigated - probably not graph related).  
  
Hopefully that is some info to think about how to solve it.   
  
> Anyway, I'm happy to work together to find a great solution.  
  
Very nice! Yes, I'm also happy - but that will not be this year, I'm afraid, due to other occupancies at this moment...

---

## Comment 7

> Username: jeremy-murphy  
> Created at: 2025-10-27 00:04:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434#issuecomment-3449034573  

Oh, looks like I was foolishly optimistic, because of course CMake resolves dependencies at the library level and that breaks with a cycle in the dependency graph: https://github.com/boostorg/graph/actions/runs/18480093442/job/53707423023?pr=441  
The quickest, slightly hacky solution is that I'll put our planar graph algorithms into a separate library in the CMake build, moving the geometry dependency out of the main Graph library.

---

## Comment 8

> Username: jeremy-murphy  
> Created at: 2025-10-27 22:51:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434#issuecomment-3453631369  

Ah, and now I know that it is actually a problem for b2 as well: https://github.com/boostorg/graph/actions/runs/18858148168/job/53810760440?pr=441

---

## Comment 9

> Username: jeremy-murphy  
> Created at: 2025-10-27 23:07:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434#issuecomment-3453668112  

I wonder if it might be possible to actually move [the function](https://github.com/jeremy-murphy/graph/blob/6c78c841564d6045fee95b91d490a2dcfa5824be/include/boost/graph/is_straight_line_drawing.hpp) that Graph uses Geometry for, into Geometry? Or would it be too unrelated to everything else?

---

## Comment 10

> Username: barendgehrels  
> Created at: 2025-10-28 18:41:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434#issuecomment-3457979610  

> I wonder if it might be possible to actually move [the function](https://github.com/jeremy-murphy/graph/blob/6c78c841564d6045fee95b91d490a2dcfa5824be/include/boost/graph/is_straight_line_drawing.hpp) that Graph uses Geometry for, into Geometry? Or would it be too unrelated to everything else?  
  
I looked into it, interesting idea. But it won't solve the mutual dependency? Because you still will call that new function from Boost.Geometry...  
Actually, it might create another one (as long as the Graph is in that function), unless we revise it.

---

## Comment 11

> Username: jeremy-murphy  
> Created at: 2025-10-29 04:24:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434#issuecomment-3459568144  

Oh, it would actually solve it, because that function is a 'leaf' algorithm us, it doesn't get used by anything else. It only exists for users to, optionally, validate answers given by the Chrobak-Payne Straight Line Drawing algorithm. So if we moved it to Geometry, then we would update our documentation to say, "See Boost.Geometry for an algorithm that can validate the geometry of the straight line drawing." We would still depend on Boost.Geometry _existing_, but no longer a code dependency.  :)

---

## Comment 12

> Username: barendgehrels  
> Created at: 2025-10-29 16:22:33 UTC  
> Updated at: 2025-10-29 16:23:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434#issuecomment-3462561835  

> Oh, it would actually solve it, because that function is a 'leaf' algorithm us  
  
Right, indeed, that sounds good. It will give us a public dependency (on the Graph) but I don't think that is an issue, given the fact that we already depend on it.  
  
>Or would it be too unrelated to everything else?  
  
It's certainly an outlier but it still fits in the kind of things we do. This would be the first on Graph.  
  
Do you have a sample, or image, of what it exactly does? (I read through the code and can make something out of it but an image, especially with graph on it, would help)  
  
We can discuss it.  
  
@vissarion @awulkiew @tinko92 what do you think?

---

## Comment 13

> Username: tinko92  
> Created at: 2025-11-27 17:09:49 UTC  
> Updated at: 2025-11-28 02:58:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434#issuecomment-3586822141  

@barendgehrels  Sorry for the late reply. Reading this graph algorithm, it depends on Boost.Geometry strictly for the crosses predicate for cartesian, double-coordinate linestrings, which are strictly single line segments. Regarding moving it to BG: It seems quite specific to graphs and to conflict with similarly named notions in BG e.g. OGC spec would not call a geometry simple if it contains identical segments but is_simple_line_drawing allows parallel edges if I understand correctly.  
  
Reading the documentation versus implementation raises some question for me @jeremy-murphy . I am not familiar with the algorithm, so my questions may be based on misunderstandings:  
  
1. In math, your special case of crosses would be rather short function (unlike the implementation for general linestrings) that might arguably not warrant pulling in the entire template machinery of BG's relate. In double, there are some numerical issues. I understand that https://github.com/boostorg/graph/issues/388 motivated the use of BG and BG handles those with robust predicates. But your Chrobak-Payne Straight Line Drawing strictly outputs integer coordinates so BG's robust predicates may still be overkill. Maybe a standalone implemention of crosses for 2 straight line segments using Boost.Multiprecision int128_t would be the simplest solution here (and improve compile times). If so, I could write a PR.  
  
2. BG implements a very specific notion of "crosses" based on OGC Simple Features. [(0,0), (0,3)] and [(0,1), (0,2)] do not cross but based on my understanding of Chrobak-Payne Straight Line Drawing, this would be invalid output for your algorithm and should be caught. Shouldn't the test actually be something like relate matches FF*F***** || equal (since parallel edges are allowed).  
  
Consider e.g. (assuming point has x and y fields that are no larger than 2^63)  
```cpp  
#include <boost/multiprecision/cpp_int.hpp>  
#include <algorithm>  
  
using int128_t  = boost::multiprecision::int128_t;  
  
int orientation2d(int128_t ax, int128_t ay, int128_t bx, int128_t by, int128_t cx, int128_t cy) {  
    auto detleft = (bx - ax) * (cy - ay);  
    auto detright = (by - ay) * (cx - ax);  
    return detleft > detright ? 1 : (detleft == detright ? 0 : -1);  
}  
  
bool between(const point& a, const point& b, const point& c) {  
    return (b.y == a.y ?   
          std::min(a.x, b.x) < c.x && c.x < std::max(a.x, b.x)   
        : std::min(a.y, b.y) < c.y && c.y < std::max(a.y, b.y));  
}  
  
bool violates_straight_line_drawing(const point& p1, const point& p2,  
                                    const point& q1, const point& q2) {  
    auto o1 = orientation2d(p1.x, p1.y, p2.x, p2.y, q1.x, q1.y);  
    auto o2 = orientation2d(p1.x, p1.y, p2.x, p2.y, q2.x, q2.y);  
    auto o3 = orientation2d(q1.x, q1.y, q2.x, q2.y, p1.x, p1.y);  
    auto o4 = orientation2d(q1.x, q1.y, q2.x, q2.y, p2.x, p2.y);  
  
    if ((o1 * o2 < 0) && (o3 * o4 < 0)) return true;  
  
    if (o1 == 0 && between(p1, p2, q1)) return true;  
    if (o2 == 0 && between(p1, p2, q2)) return true;  
    if (o3 == 0 && between(q1, q2, p1)) return true;  
    if (o4 == 0 && between(q1, q2, p2)) return true;  
    return false;  
}  
```  
  
to replace https://github.com/jeremy-murphy/graph/blob/6c78c841564d6045fee95b91d490a2dcfa5824be/include/boost/graph/is_straight_line_drawing.hpp#L32 until L57. If I don't miss anything, this catches e.g.  
[(0,0),(3,0)], [(1,-1),(1,1)] (`(o1 * o2 < 0) && (o3 * o4 < 0)`)  
[(0,0),(3,0)], [(1,0),(2,0)] (`o1 == 0 && between(p1, p2, q1)`, would be missed by `boost::geometry::crosses`)  
[(0,0),(3,0)], [(0,0),(2,0)] (`o2 == 0 && between(p1, p2, q2)`)  
[(1,1),(2,0)], [(0,0),(3,3)] (`o3 == 0 && between(q1, q2, p1)`)  
  
But does not flag  
[(0, 0),(3,0)], [(0, 0),(3,0)] (parallel edges)  
[(0, 0),(3,0)], [(3,0),(0, 0)] (parallel edges).  
  
Would this be the correct test for your function?

---

## Comment 14

> Username: jeremy-murphy  
> Created at: 2025-11-30 22:25:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434#issuecomment-3593658718  

@grafikrobot FYI, this is now resolved thanks to @tinko92. Graph won't depend on Geometry.

---

## Comment 15

> Username: barendgehrels  
> Created at: 2025-12-01 07:25:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1434#issuecomment-3595004758  

@tinko92 super!!! This is a really great answer, thanks a lot, and immediately implemented by @jeremy-murphy   
  
@jeremy-murphy thanks for handling it quickly and I'm really glad the mutual dependency is now solved.
