# #683 - Convex Hull gives wrong output for 3D points. [Open]

> Username: sudo-panda  
> Created at: 2020-03-14 04:55:50 UTC  
> Updated at: 2021-02-24 10:54:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/683  

The Graham Andrew strategy has been implemented for 2D cartesian geometry but it doesn't assert if the supplied geometry is 2D and gives wrong output when supplied with 3D geometry.

---

## Comment 1

> Username: vissarion  
> Created at: 2020-03-16 12:50:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-599518640  

Thanks for noticing. Could you please add a minimal example to reproduce this behaviour.

---

## Comment 2

> Username: sudo-panda  
> Created at: 2020-03-16 21:14:16 UTC  
> Updated at: 2020-03-16 21:31:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-599761305  

@vissarion , yeah sure. The below example uses a 3D multipoint as an input and gives out a 3D multipoint as an output. The input provided is that contains the corners of a cube and the centroid of it.  
  
```C++  
#include <iostream>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/geometry/geometries/adapted/boost_tuple.hpp>  
  
BOOST_GEOMETRY_REGISTER_BOOST_TUPLE_CS(cs::cartesian)  
  
int main()  
{  
  typedef boost::tuple<double, double, double> point;  
  typedef boost::geometry::model::multi_point<point> multi_pt;  
  
  multi_pt input;  
  boost::geometry::read_wkt("multipoint((0 0 0), (1 1 1), (1 1 0), (1 0 1), (0 1 1), (0 0 1), (0 1 0), (1 0 0), (0.5 0.5 0.5))", input);  
  
  multi_pt hull;  
  boost::geometry::convex_hull(input, hull);  
  
  using boost::geometry::wkt;  
  std::cout  
    << "input: " << wkt(input) << std::endl  
    << "hull: " << wkt(hull) << std::endl  
    ;  
  
  return 0;  
}  
```  
  
The output that we get is:  
```  
input: MULTIPOINT((0 0 0),(1 1 1),(1 1 0),(1 0 1),(0 1 1),(0 0 1),(0 1 0),(1 0 0),(0.5 0.5 0.5))  
hull: MULTIPOINT((0 0 0),(0 1 1),(1 1 1),(1 0 1),(0 0 0))  
```  
  
While the hull should contain all the points except `(0.5 0.5 0.5)`.  
  
Also in my opinion if the supplied output geometry is multipoint we should not append the start point at the end as it is self implied. it should be added only when a polygon of ring type is provided. Although I am unfamiliar with the design rationale that was there when this was implemented.  
  
Since Graham Andrew Strategy cannot be extended to 3D space I would like to implement Chan's Algorithm for cartesian geometry. I am applying for the 2nd Project in GSoC and I think it will be a good base for the project and can be extended to cover non-cartesian systems. What is your opinion on this?

---

## Comment 3

> Username: vissarion  
> Created at: 2020-03-19 13:08:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-601167983  

It probably discards the 3rd coordinate and computes the convex hull of the projection in the first two. However, the user should be notified for this behaviour. This notification should be the same for all algorithms that does not handle correctly the 3d geometries.   
  
Regarding, 3D convex hull algorithms. You should do some research on algorithms and implementations if you want to add it to your application or implement it at some point. As far as I know currently one of the best choices (performance-wise) is `qhull` (http://www.qhull.org/) which implements the quickhull algorithm (https://en.wikipedia.org/wiki/Quickhull).

---

## Comment 4

> Username: sudo-panda  
> Created at: 2020-03-19 14:56:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-601225916  

I think we should give a compile time warning stating that `Supplied argument(s) is/are 3D but will be treated as 2D`. We can generalize the template stating _supplied type_ isn't supported and will revert to a _lower type_. Once we agree on what is best I will start working on adding the functionality.  
  
As for the 3D convex hull algorithms, after thinking it over I think it better to add **Quickhull** ( _O(n log n)_, worst case _O(n<sup>2</sup>)_) as 3D implementations of it exist widely while implementations of **Chan's Algorithm** ( _O(n log h)_ ) in 3D are almost non existent. If you give the go ahead I would implement a Quickhull strategy for 2D, 3D and, if I can, 4D convex hulls too.

---

## Comment 5

> Username: Siddharth-coder13  
> Created at: 2020-12-18 06:40:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-747901406  

Hey, @vissarion I want to work on this issue and implement the Quickhill algorithm for 3D points and also work on other algorithms in general that do not handle 3D geometries correctly.

---

## Comment 6

> Username: vissarion  
> Created at: 2020-12-18 09:47:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-747989685  

Hi @Siddharth-coder13, sure you can create an PR on 3D convex hull algorithm and discuss the details there.

---

## Comment 7

> Username: barendgehrels  
> Created at: 2020-12-18 17:34:43 UTC  
> Updated at: 2020-12-18 17:35:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-748221831  

Hi @Siddharth-coder13 , thanks for your intention!  
  
There are many other algorithms not handling 3d (at all, in general in our library). It will be a huge effort. The best is to start with relatively simple ones, and convex hull would, indeed, probably be not the most complex one (though I would start with `volume` probably).  
  
However, the best first point to start with, is defining a `geometry` (model, and concept) of a polyhedral surface. Because it's not there yet and you will need it as a result of the convex hull in 3D.  
  
A second point to start with (apart from Volume or ConvexHull) could be `WKT`, a.o. because we always use them in unit tests.  
  
So this would be `POLYHEDRALSURFACE `  
https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry

---

## Comment 8

> Username: vissarion  
> Created at: 2020-12-18 18:17:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-748240914  

Indeed, as @barendgehrels note the output of 3D convex hull cannot be just an ordered list (opposed to 2D) so you need a new geometry. For `volume` things could be a bit simpler since the input could be a 3d pointset and the output a numeric value.

---

## Comment 9

> Username: Siddharth-coder13  
> Created at: 2020-12-19 07:12:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-748432510  

It seems that I have to implement `volume` by scratch. So, I will make a separate header file in the `algorithms` directory and implement its functions, Is it fine?

---

## Comment 10

> Username: barendgehrels  
> Created at: 2020-12-19 13:23:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-748474496  

>   
>   
> It seems that I have to implement `volume` by scratch. So, I will make a separate header file in the `algorithms` directory and implement its functions, Is it fine?  
  
Sure, that sounds right!

---

## Comment 11

> Username: vissarion  
> Created at: 2020-12-21 07:52:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-748825281  

Indeed, as @barendgehrels note the output of 3D convex hull cannot be just an ordered list (opposed to 2D) so you need a new geometry. So an alternative starting point could be `volume` of an input 3d pointset which outputs a numeric value. But I agree that the best start is to define the polyhedral surface.

---

## Comment 12

> Username: Siddharth-coder13  
> Created at: 2020-12-24 10:45:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-750843669  

@vissarion will you please explain about defining polyhedral surfaces, I'm not getting any clue on how to start?

---

## Comment 13

> Username: vissarion  
> Created at: 2020-12-24 14:52:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-750903057  

You can start by reading the OGC standard, and see how this is done in  
postGIS.  
  
On Thu, Dec 24, 2020, 12:45 Siddharth Kumar <notifications@github.com>  
wrote:  
  
> @vissarion <https://github.com/vissarion> will you please explain about  
> defining polyhedral surfaces, I'm not getting any clue on how to start?  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/geometry/issues/683#issuecomment-750843669>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA35UTQZFZYCL67TOML3GNLSWMLTXANCNFSM4LIG2YDA>  
> .  
>

---

## Comment 14

> Username: ayushgupta138  
> Created at: 2021-02-23 10:48:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-784110115  

Hey, @vissarion, @barendgehrels  I am interested in implementing `volume` for 3d pointsets. Is that already done? If not could I work on the same?

---

## Comment 15

> Username: vissarion  
> Created at: 2021-02-23 10:56:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-784114530  

> Hey, @vissarion, @barendgehrels I am interested in implementing `volume` for 3d pointsets. Is that already done? If not could I work on the same?  
  
@ayushgupta138 do you have a plan on how to proceed? It seems related/dependent on https://github.com/boostorg/geometry/pull/789

---

## Comment 16

> Username: Siddharth-coder13  
> Created at: 2021-02-23 12:53:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-784180426  

well, I have been working on the polyhedral surfaces for few months, for defining the `volume` of 3d figures, geometry needs to be defined. #789 is related to the same issue, I have already defined geometry for polyhedral surfaces including their wkt read and write features, and I am also working on its volume implementation.

---

## Comment 17

> Username: ayushgupta138  
> Created at: 2021-02-23 13:36:30 UTC  
> Updated at: 2021-02-23 14:37:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-784207909  

@Siddharth-coder13  what else have been working on implementing?

---

## Comment 18

> Username: ayushgupta138  
> Created at: 2021-02-24 05:11:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-784771223  

Since @Siddharth-coder13  is working on volume implementation, @vissarion  can I work on surface area and distance implementation for 3d pointsets after #789 is merged? The implementation of surface area would not require much algorithmic implementation as distance, but for distance implementation I would have to do some research.

---

## Comment 19

> Username: vissarion  
> Created at: 2021-02-24 10:54:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/683#issuecomment-784991097  

>Since @Siddharth-coder13 is working on volume implementation, @vissarion can I work on surface area and distance implementation for 3d pointsets after #789 is merged? The implementation of surface area would not require much algorithmic implementation as distance, but for distance implementation I would have to do some research.  
  
Distance in 3d seems independent from volume and could be a project to work on after #789 is merged. Surface area sounds also useful. Note that there is a connection between surface area and volume for closed polyhedral surfaces (see e.g. 15.2.1 in http://www.csun.edu/~ctoth/Handbook/chap15.pdf).
