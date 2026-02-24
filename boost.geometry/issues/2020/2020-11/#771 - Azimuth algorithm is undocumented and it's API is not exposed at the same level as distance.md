# #771 - Azimuth algorithm is undocumented and it's API is not exposed at the same level as distance [Closed]

> Username: kenba  
> Created at: 2020-11-03 08:45:18 UTC  
> Updated at: 2021-02-16 14:39:24 UTC  
> Closed at: 2021-02-16 14:38:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/771  

The OP of this stack overflow question: [boost geometry angle between two points](https://stackoverflow.com/questions/64639137/boost-geometry-angle-between-two-points) proposed using the `azimuth` algorithm to calculate the angle (bearing) between two points.   
  
The `azimuth` algorithm is only defined in the file <boost/geometry/algorithms/detail/azimuth.hpp>, it is not exposed on the API at the same level as `distance`: <boost/geometry/algorithms/distance.hpp> nor is it listed in the documentation: [algorithms](https://www.boost.org/doc/libs/1_74_0/libs/geometry/doc/html/geometry/reference/algorithms.html). A commenter pointed out that it is therefore an "undocumented" algorithm.  
  
For the purposes of navigation, the `azimuth` algorithm is as important, if not more so, than the `distance` algorithm. Therefore it's API and documentation should at least match that of the `distance` algorithm. Note, also for the purposes of navigation it should include geographic azimuth algorithms, e.g.: Andoyer, Vincenty and especially, Karney.  
  
BTW the `course` algorithm in <boost/geometry/algorithms/detail/course.hpp> simply refers to the `azimuth` algorithm. Should this also be exposed properly on the API and documented or should it be deleted?

---

## Comment 1

> Username: vissarion  
> Created at: 2020-11-03 10:17:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/771#issuecomment-721026151  

Indeed azimuth is computed internally in many places of the library but there is no (proper) algorithm exposed on the API. Such an algorithm would look similar to distance and use strategies to choose between andoyer, thomas, vincenty or karney "formulas" in geographic systems.  
  
 >BTW the course algorithm in <boost/geometry/algorithms/detail/course.hpp> simply refers to the azimuth algorithm. Should this also be exposed properly on the API and documented or should it be deleted?  
  
It is left there for backward compatibility but it seems that it is not used at all.
