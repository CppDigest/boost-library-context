# #1208 - Convex hull: wrong results (counter-example on 4 points) [Closed]

> Username: atalon-lip6  
> Created at: 2023-10-16 15:04:44 UTC  
> Updated at: 2024-04-01 11:29:53 UTC  
> Closed at: 2023-10-17 13:58:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/1208  

Hello,  
In our project (topology-toolkit) we wanted to use Boost instead of Qhull to have fewer third party libraries. We had some bug appearing when using Boost implemantation for convex hull, and after some investigation I found a minimal counter-example.  
  
Three points are aligned, and a fourth one (a2596) make a 90° angle with this line. Whether or not the middle point of the vertical segment is included may depend on precision errors.  
However, the point with largest x-coordinates should obviously be taken. Boost does not include it and includes the middle point in the convex hull, hence it is wrong.  
  
I static cast doubles into doubles because it could happen in our program. If we remove the static_casts, then the output is still wrong but boost returns different points (not taking the point with larger y-coordinate):  
0.0285095558699018,0  
0.0469472760530917,0  
0.0285095558699018,0  
0.0285095558699018,0  
  
Here is the cpp code of the example I am talking about:  
```  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/geometry/geometries/adapted/boost_tuple.hpp>  
  
#include <iostream>  
  
  
namespace bg = boost::geometry;  
BOOST_GEOMETRY_REGISTER_BOOST_TUPLE_CS(cs::cartesian)  
  
using Point = boost::tuple<double, double>;  
using Polygon =  boost::geometry::model::polygon<Point>;  
using Mpoints =  boost::geometry::model::multi_point<Point>;  
  
int main(void)  
{  
  std::cout << std::setprecision(15);  
  
  double a2738[2] = {0.028509555869901827,0.028509555869901813};  
  double a2899[2]= {0.02850955586990183, 0.017233466136418303};  
  double a2537[2] = {0.02850955586990183, 0};  
  double a2596[2] = {0.046947276053091694, 0};  
  
  Mpoints multiPoints;  
  Polygon hull;  
  
  boost::geometry::append(multiPoints, Point(static_cast<double> (a2738[0]), static_cast<double>(a2738[1])));  
  
  boost::geometry::append(multiPoints, Point(static_cast<double> (a2899[0]), static_cast<double>(a2899[1])));  
  boost::geometry::append(multiPoints, Point(static_cast<double> (a2537[0]), static_cast<double>(a2537[1])));  
  boost::geometry::append(multiPoints, Point(static_cast<double> (a2596[0]), static_cast<double>(a2596[1])));  
  
  for (const auto &pt : multiPoints)  
    std::cout << "Point:  " << pt.get<0>() << "," << pt.get<1>() << std::endl;  
  boost::geometry::convex_hull(multiPoints, hull);  
  for (const auto &boostPt : hull.outer())  
  {  
    std::cout << "In hull: " << boostPt.get<0>() << "," << boostPt.get<1>() << std::endl;  
  }  
  
  return 0;  
}  
  
```

---

## Comment 1

> Username: tinko92  
> Created at: 2023-10-16 18:02:09 UTC  
> Updated at: 2023-10-16 18:13:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/1208#issuecomment-1765001303  

I get the following output (with setprecision(17), 15 is insufficient to guarantee that different double values are printed differently, spaces changed for readability)  
```  
Point:   0.028509555869901827, 0.028509555869901813  
Point:   0.02850955586990183,  0.017233466136418303  
Point:   0.02850955586990183,  0  
Point:   0.046947276053091694, 0  
In hull: 0.028509555869901827, 0.028509555869901813  
In hull: 0.046947276053091694, 0  
In hull: 0.02850955586990183,  0  
In hull: 0.028509555869901827, 0.028509555869901813  
```  
when I change the code to use an exact side predicate (#1209, does this fix it for your build as well?).  
  
I'm curious about the changing behavior without the static_cast. What are the compiler and the command line that make the static_cast change the output? With GCC 13 and Clang 16 and -O{1,2,3} the produced assembly between casting and not casting is identical for me and even without optimization the difference just seems to be in lea and movsd instructions, no change to the output for me.

---

## Comment 2

> Username: atalon-lip6  
> Created at: 2023-10-17 12:35:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/1208#issuecomment-1766329035  

Hello! Thank you for your quick reply. First, indeed it solved the problem on these points! And I made a mistake when I removed the static_cast, so there was no difference as you guessed whether I put it or not.  
  
I may have some other problem with aligned points. In the following example, Boost returns a hull which is not minimal (boost's points are the one in pink).  
![screen-convex-hull](https://github.com/boostorg/geometry/assets/121945535/8c3ac966-b829-4bce-82bd-08f2c84322db)  
  
Without your patch, Boost selected the right three points. But maybe it's about precision and this is not really a bug.   
  
  
In Python I tried to figure out if the points are aligned: they seem so to 10^-14):  
>>> p1 = (-0.13061234329832280276, 0)  
>>> p2 = (-0.22076807253901337136, -0.090155729240690540838)  
>>> p3 = (-0.17999140530708637731, -0.049379062008763574543)  
>>> p4 = (-0.073391951989631901143, 0.057220391308690880805)  
>>> sub(p1,p2)  
(0.09015572924069057, 0.09015572924069054)  
>>> sub(p1,p3)  
(0.049379062008763575, 0.049379062008763575)  
>>> sub(p1,p4)  
(-0.0572203913086909, -0.05722039130869088)  
  
  
Here is the updated example code:  
```  
// Boost includes  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/geometry/geometries/adapted/boost_tuple.hpp>  
  
#include <iostream>  
  
  
namespace bg = boost::geometry;  
BOOST_GEOMETRY_REGISTER_BOOST_TUPLE_CS(cs::cartesian)  
  
using Point = boost::tuple<double, double>;  
using Polygon =  boost::geometry::model::polygon<Point>;  
using Mpoints =  boost::geometry::model::multi_point<Point>;  
  
int main(void)  
{  
  std::cout << std::setprecision(15);  
  
  double tab[7][2] = {  
    {-0.0382554696454134452, -0.0923568736529093715},  
    {0.00165429548851042663, -0.132266638786833257},  
    {-0.0829785267859002584, -0.0476338165124225582},  
    {-0.130612343298322803, 0},  
    {-0.220768072539013371, -0.0901557292406905408},  
    {-0.179991405307086377, -0.0493790620087635745},  
    {-0.0733919519896319011, 0.0572203913086908808}  
  };  
  
  
  Mpoints multiPoints;  
  Polygon hull;  
  
  for (int i = 0; i < 7; i++)  
    boost::geometry::append(multiPoints, Point(tab[i][0], tab[i][1]));  
  
  for (const auto &pt : multiPoints)  
    std::cout << "Point\t" << pt.get<0>() << ", " << pt.get<1>() << std::endl;  
  boost::geometry::convex_hull(multiPoints, hull);  
  for (const auto &boostPt : hull.outer())  
  {  
    std::cout << "Hull\t" << boostPt.get<0>() << ", " << boostPt.get<1>() << std::endl;  
  }  
  
  return 0;  
}  
  
  
  
```

---

## Comment 3

> Username: tinko92  
> Created at: 2023-10-17 13:26:31 UTC  
> Updated at: 2023-10-17 13:47:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/1208#issuecomment-1766417315  

As you suspect, if you go by the precise values of the closest floating-point coordinates to those decimals, then p1, p2 and p3 are not exactly collinear. The following image shows the very close neighborhood of the floating-point approximation of p3 in blue or red depending on their relation to the straight line through the floating-point approximations of p1 and p2. p3 is the rectangle with the black point.   
![non_collinear](https://github.com/boostorg/geometry/assets/17624459/3094332d-1d60-4497-a834-64e2a97911b3)  
  
I think there is no solution to this without downsides. If you calculate the side predicate that convex hull uses exactly, then it will consider those almost collinear-points non-collinear, even though they may be collinear up to the resolution of the floating-point grid. If you round the side predicate to zero for small values, the convex hull can become entirely invalid for difficult inputs (there is detailed discussion of that here: https://people.mpi-inf.mpg.de/~mehlhorn/ftp/classroomExamplesNonrobustness.pdf with an example of a point being missed in the convex hull due to rounding to zero. Their convex hull algorithm is different from BG, though, so I don't know how well the exact arguments apply).  
  
Maybe a viable solution for you would be to use the patch for the exact side-predicate and do one more post-processing step afterwards with a rounding side predicate to eliminate almost-collinear points on edges from the result. Here is a tolerant side predicate that you could use for post-processing:  
  
```cpp  
struct side_rounded_input  
{  
    template <typename P1, typename P2, typename P>  
    static inline int apply(P1 const& p1, P2 const& p2, P const& p)  
    {  
        using namespace boost::geometry;  
        double ax = get<0>(p1);  
        double ay = get<1>(p1);  
        double bx = get<0>(p2);  
        double by = get<1>(p2);  
        double cx = get<0>(p);  
        double cy = get<1>(p);  
  
        double det = (ax - cx) * (by - cy) - (ay - cy) * (bx - cx);  
        constexpr double eps = std::numeric_limits<double>::epsilon() / 2;  
        const double err_bound = (5 * eps + 32 * eps * eps) *  
            (  (std::abs(ax) + std::abs(cx)) * (std::abs(by) + std::abs(cy))  
             + (std::abs(bx) + std::abs(cx)) * (std::abs(ay) + std::abs(cy)));  
        return (det > err_bound) - (det < -err_bound);  
    }  
};  
```  
If the input points are collinear up the error of representing the decimal coordinates as double, then it will find them collinear (returns 0). It may also find points collinear that are further away from collinear as caused by being represented in double to account for rounding errors in the computation. If the coordinates of your points are the results of computations that could have more accumulated error than just the error caused by representing them in double, you could account for that by changing the factor 5 before eps to some greater number.  
  
To give you a rough idea, this is how the neighborhood would look under this tolerant side predicate.  
![rounded_inputs](https://github.com/boostorg/geometry/assets/17624459/172eaf53-1a22-40b2-8899-d3c95a7050c0)  
  
The width of those squares is around 3e-17.

---

## Comment 4

> Username: atalon-lip6  
> Created at: 2023-10-17 13:58:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/1208#issuecomment-1766477633  

Ok, thank you for your detailed answer!  
I agree with you.
