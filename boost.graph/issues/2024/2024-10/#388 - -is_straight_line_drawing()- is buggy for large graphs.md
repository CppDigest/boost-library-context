# #388 - "is_straight_line_drawing()" is buggy for large graphs [Closed]

> Username: Hermann-SW  
> Created at: 2024-10-04 22:33:42 UTC  
> Updated at: 2025-01-26 21:22:47 UTC  
> Closed at: 2025-01-26 21:22:47 UTC  
> Url: https://github.com/boostorg/graph/issues/388  

I experienced "is_straight_line_drawing()" returning <kbd>false</kbd> for valid straight line drawing of a maximal planar graph with 10million vertices first:    
https://lists.boost.org/Archives/boost/2024/10/257979.php  
  
Later I was able to reduce to 1million vertices, and now to only three vertices with this simplified gist:    
https://gist.github.com/Hermann-SW/992b43eef29f1ef28c4bf2727f0a7c16  
  
After the straight line drawing for the K₃ was computed, I did overwrite the determined coordinates for the three vertices with the coordinates of three vertices reported for initial 10million vertex graph. With that coordinates and the edges in same direction as in previous graph the problem is recreated fast:  
```  
hermann@7950x:~$ f=is_straight_line_drawing.recreate  
hermann@7950x:~$ g++ -O3 -Wall -pedantic -Wextra $f.cpp -o $f  
hermann@7950x:~$ ./is_straight_line_drawing.recreate  
is_straight_line_drawing.recreate: is_straight_line_drawing.recreate.cpp:101: int main(int, char**): Assertion `is_straight_line_drawing(g, straight_line_drawing)' failed.  
Aborted (core dumped)  
hermann@7950x:~$   
```  
  
The problem is that "is_straight_line_drawing()" working on coordinates of std::size_t does call function using double, causing false report:  
```  
inline bool intersects(double x1, double y1, double x2, double y2, double a1,  
     double b1, double a2, double b2, double epsilon = 0.000001)  
{   
```  
  
There are plenty of intersection test algorithms that avoid doing division and report correct result for integer coordinates, eg:    
1. https://en.wikipedia.org/wiki/Line%E2%80%93line_intersection#Given_two_points_on_each_line_segment  
2. https://www.dcs.gla.ac.uk/~pat/52233/slides/Geometry1x1.pdf#page=4  
  
"bool intersects()" needs to be implemented with integer coordinates for correct response.  
  
This is the graph from recreate gist:  
```  
    graph g(3);  
    add_edge(0, 1, g);  
    add_edge(2, 0, g);  
    add_edge(1, 2, g);  
```  
  
And these are the coordinates:  
```  
    straight_line_drawing[0].x = 4143438;  
    straight_line_drawing[0].y = 86426;  
    straight_line_drawing[1].x = 4064945;  
    straight_line_drawing[1].y = 7932;  
    straight_line_drawing[2].x = 4064944;  
    straight_line_drawing[2].y = 7931;  
```  
  
There is a very small angle between edge 0--1 and edge 2--0 at vertex 0, with slope of edges 78494/78493 and 78495/78494, which cannot be correctly handled by double type function:    
```  
4143438-4064945 = 78493  
86426-7932 = 78494  
  
4143438-4064944 = 78494  
86426-7931 = 78495  
```

---

## Comment 1

> Username: sehe  
> Created at: 2024-10-05 02:34:44 UTC  
> Updated at: 2024-10-05 02:42:53 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2394869835  

> Later I was able to reduce to 1million vertices, and now to only three vertices with this simplified gist:  
> [gist.github.com/Hermann-SW/992b43eef29f1ef28c4bf2727f0a7c16](https://gist.github.com/Hermann-SW/992b43eef29f1ef28c4bf2727f0a7c16)  
  
I suggest the minimized version [Live On Coliru](https://coliru.stacked-crooked.com/a/53e401dffa7714ae	)  
  
```c++  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/is_straight_line_drawing.hpp>  
  
int main() {  
    boost::adjacency_list<boost::vecS, boost::vecS, boost::undirectedS> g;  
    add_edge(0, 1, g);  
    add_edge(2, 0, g);  
    add_edge(1, 2, g);  
  
    struct coord_t { size_t x, y; };  
    std::vector<coord_t> coordinates{{4143438, 86426}, {4064945, 7932}, {4064944, 7931}};  
    assert(is_straight_line_drawing(g, coordinates.data())); // FAILS  
}  
```  
  
After all, you're not depending on the algorithms, you're just hardcoding three points taken from a larger output.

---

## Comment 2

> Username: sehe  
> Created at: 2024-10-05 02:41:57 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2394871508  

> "bool intersects()" needs to be implemented with integer coordinates for correct response.  
  
It's unclear to me how you want to achieve that. The acceptable tolerance is subjective/context dependent at best. In any solution I can imagine, one would still have to use some given tolerance. I'd suggest that you want to be able to specify the precision of the calculation type and tolerance of the comparison.

---

## Comment 3

> Username: mglisse  
> Created at: 2024-10-05 06:34:33 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2394950582  

At the very least the doc and code don't match, the doc says that `x` and `y` must be implicitly convertible to `size_t` but the code also relies on an implicit conversion to `double`. I completely agree that the intersection test could be performed exactly with integers (they don't even need to be huge, roughly twice the size of the input). I don't know if you want to introduce a dependency to Boost.Multiprecision or Boost.Geometry for this intersection test though.

---

## Comment 4

> Username: Hermann-SW  
> Created at: 2024-10-05 06:37:54 UTC  
> Updated at: 2024-10-05 06:52:50 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2394951350  

> > "bool intersects()" needs to be implemented with integer coordinates for correct response.  
>   
> It's unclear to me how you want to achieve that. The acceptable tolerance is subjective/context dependent at best. In any solution I can imagine, one would still have to use some given tolerance. I'd suggest that you want to be able to specify the precision of the calculation type and tolerance of the comparison.  
  
Very simple, eg. with first Wikipedia link algorithm I provided:    
>  These inequalities can be tested without the need for division, allowing rapid determination of the existence of any line segment intersection before calculating its exact point.  
>  
So while the formula shows a faction, the nominator and denominator are integers because the the straight line drawing coordinates are (convertible to) std::size_t. A test whether t and u are in [0..1] can be done without division, just with integer comparison in integer coordinate case, for denom>0 [for graph edge intersection test (0..1) needs to tested, with > and < instead]:  
```  
num >= 0 && num <= denom  
```  
  
  
The O(n*log(n)) "sweep" part of the algorithm defines then x- and y-coord types as std::size_t:  
```  
template < typename Graph, typename GridPositionMap, typename VertexIndexMap >  
bool is_straight_line_drawing(  
    const Graph& g, GridPositionMap drawing, VertexIndexMap)  
{  
  
    typedef typename graph_traits< Graph >::vertex_descriptor vertex_t;  
    typedef typename graph_traits< Graph >::edge_descriptor edge_t;  
    typedef typename graph_traits< Graph >::edge_iterator edge_iterator_t;  
  
    typedef std::size_t x_coord_t;  
    typedef std::size_t y_coord_t;  
    typedef boost::tuple< edge_t, x_coord_t, y_coord_t > edge_event_t;  
    typedef typename std::vector< edge_event_t > edge_event_queue_t;  
```  
  
So  
```  
inline bool intersects(double x1, double y1, double x2, double y2, double a1,  
    double b1, double a2, double b2, double epsilon = 0.000001)  
```  
should be changed to   
```  
inline bool intersects(std::size_t x1, std::size_t y1, std::size_t x2, std::size_t y2,  
    std::size_t a1, std::size_t b1, std::size_t a2, std::size_t b2)  
```  
without epsilon, and implemented with any of the integer coordinate precision loss free algorithms.

---

## Comment 5

> Username: Hermann-SW  
> Created at: 2024-10-05 07:09:25 UTC  
> Updated at: 2024-10-05 07:11:22 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2394959469  

> I completely agree that the intersection test could be performed exactly with integers (they don't even need to be huge, roughly twice the size of the input). I don't know if you want to introduce a dependency to Boost.Multiprecision or Boost.Geometry for this intersection test though.  
>  
Multiprecision would be overkill and slower than std::size_t.  
I tested on ARM, AMD and Intel 64bit OS, and std::size_t is 64bit for all.  
<kbd>straight_line_drawing()</kbd> generates coordinates in range 2n-4 times n.  
So differences of up to 2n are possible for n vertex graph.  
For the products to fit into 64bits, n should be ≤2³¹ which is 2 billion.  
  
While I tested straight line drawing algorithm for up to 10 million vertices for now, I plan to test much higher as well when switching on (slower) Xeon server with 384GB RAM (10 million vertices did just fit into the 32GB RAM my 7950X PC has). But even with that restriction of new function to graphs with at most 2 billion vertices would be OK for me.

---

## Comment 6

> Username: mglisse  
> Created at: 2024-10-05 07:55:49 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2394971107  

> Multiprecision would be overkill and slower than std::size_t.  
> I tested on ARM, AMD and Intel 64bit OS, and std::size_t is 64bit for all.  
  
Systems with 32-bit size_t still exist, using size_t instead of double for those may be a regression. On the other hand, in C++11, they must provide at least one 64-bit type (`long long`), so it should be ok to rely on a 64-bit type, just not necessarily called size_t.  
  
> straight_line_drawing() generates coordinates in range 2n-4 times n.  
> So differences of up to 2n are possible for n vertex graph.  
> For the products to fit into 64bits, n should be ≤2³¹ which is 2 billion.  
  
Nothing says `is_straight_line_drawing` is restricted to testing the output of `straight_line_drawing`, someone may want to generate a drawing on a larger grid so it can have additional nice properties. But if the limitation is considered acceptable (it is better than what we have now), it will have to be documented.

---

## Comment 7

> Username: Hermann-SW  
> Created at: 2024-10-07 11:18:33 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2396641861  

Agreed, a 64bit type for 32bit systems.  
But for 64bit systems product might get 128bit wide, and the Wikipedia as well as the orientation algorithm require multiplication. So for 64bit systems some multiprecision code is needed. But if that is used, it can be used for 32bit systems as well. So use gmp_int?  
https://www.boost.org/doc/libs/1_86_0/libs/multiprecision/doc/html/boost_multiprecision/tut/ints/gmp_int.html

---

## Comment 8

> Username: mglisse  
> Created at: 2024-10-07 11:36:24 UTC  
> Updated at: 2024-10-07 11:36:53 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2396679274  

> But for 64bit systems product might get 128bit wide,  
  
Unless you document and assert that the input must fit in 30 bits or so.  
  
> So for 64bit systems some multiprecision code is needed. But if that is used, it can be used for 32bit systems as well. So use gmp_int?  
  
If adding a dependency on Boost.Multiprecision is ok, I'd suggest `cpp_int` (or better yet telling cpp_int_backend the number of bits you need), this avoids a dependency on the external GMP and will perform better for small numbers (no allocation).  
  
(by the way, I am not a maintainer, just giving random advice)

---

## Comment 9

> Username: jeremy-murphy  
> Created at: 2024-10-08 04:48:27 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2398827552  

If it's a geometry calculation, I'm inclined to just use Boost.Geometry and let them decide on the best way to calculate it.

---

## Comment 10

> Username: jeremy-murphy  
> Created at: 2024-10-08 04:54:17 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2398833844  

Btw, best way to get this solved is to first open a PR that adds a failing test. Once we agree that the test is correct (meaning, it should pass), then someone (maybe you, maybe me, maybe someone else) use it to trial solutions. In this case, the more numerical edge cases the better, but even one is enough to get started.

---

## Comment 11

> Username: Hermann-SW  
> Created at: 2024-10-08 07:09:46 UTC  
> Updated at: 2024-10-08 07:36:46 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2399023380  

> Btw, best way to get this solved is to first open a PR that adds a failing test. Once we agree that the test is correct (meaning, it should pass), then someone (maybe you, maybe me, maybe someone else) use it to trial solutions. In this case, the more numerical edge cases the better, but even one is enough to get started.  
  
There was an existing testcase, so I named the new testcase "...issue.cpp":   
```  
hermann@j4105:~/graph/test$ ls -l is_straight_line_draw_*  
-rw-rw-r-- 1 hermann hermann  848 Okt  8 09:04 is_straight_line_draw_issue.cpp  
-rw-rw-r-- 1 hermann hermann 5038 Okt  2 17:10 is_straight_line_draw_test.cpp  
hermann@j4105:~/graph/test$   
```  
  
Since it fails it should not be part of Jamfile.v2 for now:  
```  
hermann@j4105:~/graph/test$ g++ is_straight_line_draw_issue.cpp   
hermann@j4105:~/graph/test$ ./a.out   
is_straight_line_draw_issue.cpp(29): test 'is_straight_line_drawing(g, coordinates.data())' failed in function 'int main()'  
1 error detected.  
hermann@j4105:~/graph/test$   
```  
  
My graph fork is blocked by outstanding PR 387.  
Can you please create the PR for this new testcase?  
```  
hermann@j4105:~/graph/test$ cat is_straight_line_draw_issue.cpp   
#include <boost/core/lightweight_test.hpp>  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/is_straight_line_drawing.hpp>  
  
int main() {  
    boost::adjacency_list<boost::vecS, boost::vecS, boost::undirectedS> g;  
  
    add_edge(0, 1, g);  
    add_edge(2, 0, g);  
    add_edge(1, 2, g);  
  
    struct coord_t { size_t x, y; };  
    std::vector<coord_t> coordinates{  
        {4143438, 86426},  
        {4064945, 7932},  
	{4064944, 7931}  
    };  
/*  
   There is a very small angle between edge 0--1 and edge 2--0 at vertex 0,  
   with slope of edges 78494/78493 != 78495/78494, which cannot be  
   correctly handled by double type function "intersects()" called  
   by "is_straight_line_drawing()":  
  
4143438-4064945 = 78493  
86426-7932 = 78494  
  
4143438-4064944 = 78494  
86426-7931 = 78495  
*/  
    BOOST_TEST(is_straight_line_drawing(g, coordinates.data()));  
  
    return boost::report_errors();  
}  
hermann@j4105:~/graph/test$   
```   
  
P.S:     
The coordinates are realistic, created for a maximal planar graph    
on 10 million vertices with  <kbd>chrobak_payne_straight_line_drawing()</kbd>.     
  
P.P.S:  
All other planar algorithms work beatifully even for 100 million vertex    
maximal planar graph, it would be nice to get this issue fixed.  
  
"[Boost] runtime+memory evaluation of 6 BGL planar graph algorithms"  
https://lists.boost.org/Archives/boost/2024/10/258012.php

---

## Comment 12

> Username: jeremy-murphy  
> Created at: 2024-10-09 05:23:26 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2401342076  

I don't really have time to create and work on PRs, sorry. I can help you with your PRs, and I think this bug is orthogonal to #387, so you can just go ahead and create a new PR specifically for it. I suggest you _do_ put the test in the Jamfile -- you essentially want a red/failing PR, because that in some sense proves that the new test is novel.

---

## Comment 13

> Username: Hermann-SW  
> Created at: 2024-10-09 10:11:25 UTC  
> Updated at: 2024-10-09 10:11:48 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2401902810  

>I can help you with your PRs, and I think this bug is orthogonal to #387,  
>  
yes.  
  
> so you can just go ahead and create a new PR specifically for it. I suggest you _do_ put the test in the Jamfile -- you essentially want a red/failing PR, because that in some sense proves that the new test is novel.  
>  
I created new "bug_is_straight_line_drawing" branch in my fork.  
But that does contain all changes from #387 , and I cannot figure out how to make this branch just take latest of boostorg/graph without my changes for the PR. So I will have to wait for #387 decision to create new PR regarding this issue.

---

## Comment 14

> Username: jeremy-murphy  
> Created at: 2024-10-10 02:04:02 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2403751453  

Ok, sounds like you're not too familiar with git? It's a very powerful and flexible tool, so I suggest reading the manual over time:  
https://git-scm.com/docs/user-manual  
Ask a friend for a good introduction video/website.   
  
What you needed to do was to create your new branch starting not from your existing feature branch, but starting from `develop`.  
You can still do it, but you need to get familiar with the switch command in git so that you can navigate around comfortably. I don't want to send you off into a trap that you can't escape from.

---

## Comment 15

> Username: jeremy-murphy  
> Created at: 2024-10-10 02:06:52 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2403753729  

@sehe do you have time?

---

## Comment 16

> Username: jeremy-murphy  
> Created at: 2024-10-11 03:57:37 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2406508459  

Actually, this is probably a pretty small task, I'll try to find time to do it.

---

## Comment 17

> Username: Hermann-SW  
> Created at: 2024-10-11 23:14:00 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2408226902  

> If it's a geometry calculation, I'm inclined to just use Boost.Geometry and let them decide on the best way to calculate it.  
  
I played with Boost.Geometry and wanted to use [intersection()](https://www.boost.org/doc/libs/1_86_0/libs/geometry/doc/html/geometry/reference/algorithms/intersection/intersection_3.html).  
  
First I tried intersection of two linestrings, but that is not implemented:    
```  
usr/include/boost/geometry/algorithms/detail/overlay/intersection_insert.hpp:560:5: error: static assertion failed: Not or not yet implemented for these Geometry types or their order.  
```  
  
So I used polygon with only "outer", no "inner".  
But that does not produce meaningful output.  
Obviously Boost.Geometry cannot deal with area=0 polygons correctly:  
```  
$ g++ g4.cpp   
$ ./a.out   
0: POLYGON((3 1,1 1,1 3,3 3,3 1))  
$  
```  
  
Intersection should be point "1 1", here is g4.cpp:  
```  
#include <iostream>  
#include <deque>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
  
#include <boost/foreach.hpp>  
  
namespace bg = boost::geometry;  
  
int main()  
{  
    typedef bg::model::d2::point_xy<std::size_t> P;  
    typedef bg::model::polygon<P> polygon;  
  
    polygon poly1, poly2;  
    bg::append(poly1.outer(), P(0,0));  
    bg::append(poly1.outer(), P(2,2));  
    bg::append(poly1.outer(), P(0,0));  
  
    bg::append(poly2.outer(), P(2,0));  
    bg::append(poly2.outer(), P(0,2));  
    bg::append(poly2.outer(), P(2,0));  
  
    std::deque<polygon> output;  
    boost::geometry::intersection(poly1, poly2, output);  
  
    int i = 0;  
    BOOST_FOREACH(polygon const& p, output)  
    {  
        std::cout << i++ << ": " << bg::to_wkt(p) << std::endl;  
    }  
  
    return 0;  
}  
```

---

## Comment 18

> Username: mglisse  
> Created at: 2024-10-12 07:05:17 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2408428696  

You may want to ask the Boost.Geometry people. They have their own [mailing list](https://lists.boost.org/mailman/listinfo.cgi/geometry), although it seems a bit dead. They have also enabled [github discussions](https://github.com/boostorg/geometry/discussions).  
It may be that you should ask for the intersection of 2 segments as a range of points, or that you should use a different function (`relate`?), or something else.  
They may also be able to confirm if it is supposed to use big integers internally automatically, or if you will need to specify it in your function call somehow.

---

## Comment 19

> Username: Hermann-SW  
> Created at: 2024-10-12 17:42:12 UTC  
> Updated at: 2024-10-12 17:43:35 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2408639600  

I learned how to create a branch for providing new testcase.  
I added new testcase at end of existing <kbd>is_straight_line_draw_test.cpp</kbd>

---

## Comment 20

> Username: Hermann-SW  
> Created at: 2024-10-12 20:00:41 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2408685101  

> You may want to ask the Boost.Geometry people.   
  
:white_check_mark:  
  
> They have their own [mailing list](https://lists.boost.org/mailman/listinfo.cgi/geometry), although it seems a bit dead.  
  
Correct.  
  
> They have also enabled [github discussions](https://github.com/boostorg/geometry/discussions).   
  
I created new discussion:  
https://github.com/boostorg/geometry/discussions/1330

---

## Comment 21

> Username: Hermann-SW  
> Created at: 2024-10-12 20:29:25 UTC  
> Url: https://github.com/boostorg/graph/issues/388#issuecomment-2408692043  

Oops, the last and only 2023 posting from mailing list has probably the solution:  
https://github.com/boostorg/geometry/discussions/1330#discussioncomment-10924846
