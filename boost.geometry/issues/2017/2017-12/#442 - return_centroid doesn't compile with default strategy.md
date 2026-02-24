# #442 - return_centroid doesn't compile with default strategy [Closed]

> Username: ArekPiekarz  
> Created at: 2017-12-27 13:11:15 UTC  
> Updated at: 2018-01-20 22:41:44 UTC  
> Closed at: 2018-01-20 22:41:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/442  

The function return_centroid with default strategy used to work in Boost 1.64.  
After recent upgrade to Boost 1.66 in Msys2 (it skipped 1.65) it stopped compiling this code:  
  
```  
#include <iostream>  
#include <boost/geometry/algorithms/centroid.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
  
using Point = boost::geometry::model::d2::point_xy<float>;  
using Polygon = boost::geometry::model::polygon<Point>;  
  
int main()  
{  
    const Polygon polygon{{Point{0,0}, Point{0,1}, Point{1,0}}};  
    const auto centroid = boost::geometry::return_centroid<Point,Polygon>(polygon);  
    std::cout << "centroid: " << centroid.x() << " " << centroid.y();  
    return 0;  
}  
```  
It fails with following errors:  
  
```  
[1/2 0.2/sec] Building CXX object CMakeFiles/boost-geometry-centroid-test.dir/main.cpp.obj  
FAILED: CMakeFiles/boost-geometry-centroid-test.dir/main.cpp.obj   
D:\app\msys64\mingw64\bin\g++.exe    -g -MD -MT CMakeFiles/boost-geometry-centroid-test.dir/main.cpp.obj -MF CMakeFiles\boost-geometry-centroid-test.dir\main.cpp.obj.d -o CMakeFiles/boost-geometry-centroid-test.dir/main.cpp.obj -c D:/dev/project/test/boost-geometry-centroid-test/main.cpp  
In file included from D:/dev/project/test/boost-geometry-centroid-test/main.cpp:2:0:  
D:/app/msys64/mingw64/include/boost/geometry/algorithms/centroid.hpp: In instantiation of 'static bool boost::geometry::detail::centroid::centroid_polygon::apply(const Polygon&, Point&, const Strategy&) [with Polygon = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >; Point = boost::geometry::model::d2::point_xy<float>; Strategy = boost::geometry::strategy::centroid::not_applicable_strategy]':  
D:/app/msys64/mingw64/include/boost/geometry/algorithms/centroid.hpp:407:32:   required from 'static void boost::geometry::detail::centroid::centroid_linear_areal<Algorithm>::apply(const Geometry&, Point&, const Strategy&) [with Geometry = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >; Point = boost::geometry::model::d2::point_xy<float>; Strategy = boost::geometry::strategy::centroid::not_applicable_strategy; Algorithm = boost::geometry::detail::centroid::centroid_polygon]'  
D:/app/msys64/mingw64/include/boost/geometry/algorithms/centroid.hpp:534:44:   required from 'static void boost::geometry::resolve_strategy::centroid<Geometry>::apply(const Geometry&, Point&, boost::geometry::default_strategy) [with Point = boost::geometry::model::d2::point_xy<float>; Geometry = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >]'  
D:/app/msys64/mingw64/include/boost/geometry/algorithms/centroid.hpp:550:52:   required from 'static void boost::geometry::resolve_variant::centroid<Geometry>::apply(const Geometry&, Point&, const Strategy&) [with Point = boost::geometry::model::d2::point_xy<float>; Strategy = boost::geometry::default_strategy; Geometry = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >]'  
D:/app/msys64/mingw64/include/boost/geometry/algorithms/centroid.hpp:608:47:   required from 'void boost::geometry::centroid(const Geometry&, Point&, const Strategy&) [with Geometry = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >; Point = boost::geometry::model::d2::point_xy<float>; Strategy = boost::geometry::default_strategy]'  
D:/app/msys64/mingw64/include/boost/geometry/algorithms/centroid.hpp:631:23:   required from 'void boost::geometry::centroid(const Geometry&, Point&) [with Geometry = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >; Point = boost::geometry::model::d2::point_xy<float>]'  
D:/app/msys64/mingw64/include/boost/geometry/algorithms/centroid.hpp:650:23:   required from 'Point boost::geometry::return_centroid(const Geometry&) [with Point = boost::geometry::model::d2::point_xy<float>; Geometry = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >]'  
D:/dev/project/test/boost-geometry-centroid-test/main.cpp:12:82:   required from here  
D:/app/msys64/mingw64/include/boost/geometry/algorithms/centroid.hpp:314:43: error: no type named 'state_type' in 'struct boost::geometry::strategy::centroid::not_applicable_strategy'  
             typename Strategy::state_type state;  
                                           ^~~~~  
D:/app/msys64/mingw64/include/boost/geometry/algorithms/centroid.hpp:314:43: error: no type named 'state_type' in 'struct boost::geometry::strategy::centroid::not_applicable_strategy'  
D:/app/msys64/mingw64/include/boost/geometry/algorithms/centroid.hpp:317:27: error: 'const struct boost::geometry::strategy::centroid::not_applicable_strategy' has no member named 'result'  
             if ( strategy.result(state, centroid) )  
                  ~~~~~~~~~^~~~~~  
ninja: build stopped: subcommand failed.  
14:02:41: The process "D:\app\CMake\bin\cmake.exe" exited with code 1.  
Error while building/deploying project boost-geometry-centroid-test (kit: Qt 5.10.0 MinGW x64 GCC 7.2)  
When executing step "CMake Build"  
```  
  
A workaround that I found is changing the code to use bashein_detmer strategy explicitly.  
  
Environment: Windows 10 x64, Msys2, GCC 7.2 x64, Boost 1.66.0 x64.

---

## Comment 1

> Username: awulkiew  
> Created at: 2017-12-27 22:27:54 UTC  
> Updated at: 2017-12-27 22:28:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/442#issuecomment-354186895  

The strategy has to be available for the compiler. You shouldn't be forced to pass it explicitly into the `centroid()` function but the header must be included. Including either of these should fix it:  
  
    #include <boost/geometry/strategies/cartesian/centroid_bashein_detmer.hpp>  
  
or (file including all strategies):  
  
    #include <boost/geometry/strategies/strategies.hpp>  
  
or (file including all algorithms and strategies):  
  
    #include <boost/geometry.hpp>  
  
Note that the default strategy for cartesian 2d areal geometries is defined here: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/cartesian/centroid_bashein_detmer.hpp#L236  
  
This is how Boost.Geometry is designed. If it worked before exactly with the headers you have in the example this was a coincidence.

---

## Comment 2

> Username: ArekPiekarz  
> Created at: 2017-12-28 11:56:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/442#issuecomment-354277402  

Including centroid_bashein_detmer.hpp or strategies.hpp like you said fixed the issue.  
  
In that case I believe the documentation should mention one of these includes is needed to define the strategy or a user has to create his own. Right now there is no mention of strategies on [return_centroid](http://www.boost.org/doc/libs/1_66_0/libs/geometry/doc/html/geometry/reference/algorithms/centroid/return_centroid_1.html) page. There is a table with behaviors, but it isn't explained how they connect to the default stategy.  
The compiler errors also don't make it obvious to deduce that a working default strategy is already defined elsewhere.  
  
If you agree, I could try creating a pull request with changes to documentation to make it easier for others trying to use return_centroid.  
  
While I was browsing docs and sources I also stumbled upon strange inconsistencies.  
I can see [return_centroid (with strategy)](http://www.boost.org/doc/libs/1_66_0/libs/geometry/doc/html/geometry/reference/algorithms/centroid/return_centroid_2_with_strategy.html) page lists only Bashein-Detmer as available one. [Strategies](http://www.boost.org/doc/libs/1_66_0/libs/geometry/doc/html/geometry/reference/strategies.html) page on the other hand contains one additional - [centroid average](http://www.boost.org/doc/libs/1_66_0/libs/geometry/doc/html/geometry/reference/strategies/strategy_centroid_average.html). But in the sources there is also [centroid weighted length](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/cartesian/centroid_weighted_length.hpp).  
Is that normal that the last one is not linked anywhere and the "return_centroid (with strategy)" mentions only one strategy?

---

## Comment 3

> Username: awulkiew  
> Created at: 2017-12-29 13:47:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/442#issuecomment-354448044  

Of course any help is appreciated. If you're willing to improve documentation I'll gladly review your pull requests and help you in case you had any problems.  
  
There are some inconsistencies and omissions for sure like the lack of strategies mentioned on algorithms pages, lack of examples, pictures, supported coordinate systems (strategies implemented for this coordinate system) etc.  
  
However note that your problem with headers is more general. This is how all algorithms and strategies work. So as general case it could be described in the "Compilation" section, probably under "Advanced Includes". Because AFAIU the standard way of including Boost.Geometry is to include everything with `#include <boost/geometry.hpp>` and only if you want to do something special and are knowledgeable enough include only needed parts of the library.  
  
Indeed describing the default strategies on `return_centroid` (or any algorithm) page could be helpful. And indeed on the `return_centroid (with_strategies)` page there is only 1 strategy mentioned. But then I'm not so sure if this is a good place to describe which headers exactly should be included in order to include certain strategy, this info should rather be on the strategy page. However it would be helpful if it was clear which strategies are the default ones for which geometries and coordinate system exactly and that you have to include them if you wanted to use them with the algorithm.
