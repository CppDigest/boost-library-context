# #443 - union_ doesn't compile with -Wduplicated-branches -Werror [Closed]

> Username: ArekPiekarz  
> Created at: 2017-12-27 13:53:18 UTC  
> Updated at: 2022-06-23 10:57:12 UTC  
> Closed at: 2022-06-23 10:57:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/443  

The following code using union_ compiled without errors in Boost 1.64, but in Boost 1.66 it fails because of duplicated branches:  
  
```  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/geometry/algorithms/union.hpp>  
  
using Point = boost::geometry::model::d2::point_xy<float>;  
using Polygon = boost::geometry::model::polygon<Point>;  
  
int main()  
{  
    const Polygon polygon1{{Point{0,0}, Point{0,1}, Point{1,0}}};  
    const Polygon polygon2{{Point{-1,0}, Point{0,1}, Point{1,0}}};  
    std::vector<Polygon> outputPolygon;  
    boost::geometry::union_(polygon1, polygon2, outputPolygon);  
  
    return 0;  
}  
```  
  
It produces the following errors:  
```  
[1/2 0.0/sec] Building CXX object CMakeFiles/boost-geometry-union-test.dir/main.cpp.obj  
FAILED: CMakeFiles/boost-geometry-union-test.dir/main.cpp.obj   
D:\app\msys64\mingw64\bin\g++.exe    -g   -Werror -Wduplicated-branches -MD -MT CMakeFiles/boost-geometry-union-test.dir/main.cpp.obj -MF CMakeFiles\boost-geometry-union-test.dir\main.cpp.obj.d -o CMakeFiles/boost-geometry-union-test.dir/main.cpp.obj -c D:/dev/project/test/boost-geometry-union-test/main.cpp  
D:/app/msys64/mingw64/include/boost/geometry/algorithms/detail/overlay/overlay.hpp: In instantiation of 'static OutputIterator boost::geometry::detail::overlay::overlay<Geometry1, Geometry2, Reverse1, Reverse2, ReverseOut, GeometryOut, OverlayType>::apply(const Geometry1&, const Geometry2&, const RobustPolicy&, OutputIterator, const Strategy&, Visitor&) [with RobustPolicy = boost::geometry::detail::robust_policy<boost::geometry::model::d2::point_xy<float>, boost::geometry::model::point<long long int, 2, boost::geometry::cs::cartesian>, float>; OutputIterator = boost::geometry::range::back_insert_iterator<std::vector<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> > > >; Strategy = boost::geometry::strategy::intersection::cartesian_segments<>; Visitor = boost::geometry::detail::overlay::overlay_null_visitor; Geometry1 = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >; Geometry2 = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >; bool Reverse1 = false; bool Reverse2 = false; bool ReverseOut = false; GeometryOut = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >; boost::geometry::overlay_type OverlayType = (boost::geometry::overlay_type)0]':  
D:/app/msys64/mingw64/include/boost/geometry/algorithms/detail/overlay/overlay.hpp:418:21:   required from 'static OutputIterator boost::geometry::detail::overlay::overlay<Geometry1, Geometry2, Reverse1, Reverse2, ReverseOut, GeometryOut, OverlayType>::apply(const Geometry1&, const Geometry2&, const RobustPolicy&, OutputIterator, const Strategy&) [with RobustPolicy = boost::geometry::detail::robust_policy<boost::geometry::model::d2::point_xy<float>, boost::geometry::model::point<long long int, 2, boost::geometry::cs::cartesian>, float>; OutputIterator = boost::geometry::range::back_insert_iterator<std::vector<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> > > >; Strategy = boost::geometry::strategy::intersection::cartesian_segments<>; Geometry1 = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >; Geometry2 = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >; bool Reverse1 = false; bool Reverse2 = false; bool ReverseOut = false; GeometryOut = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >; boost::geometry::overlay_type OverlayType = (boost::geometry::overlay_type)0]'  
D:/app/msys64/mingw64/include/boost/geometry/algorithms/union.hpp:290:20:   required from 'static void boost::geometry::resolve_strategy::union_::apply(const Geometry1&, const Geometry2&, const RobustPolicy&, Collection&, boost::geometry::default_strategy) [with Geometry1 = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >; Geometry2 = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >; RobustPolicy = boost::geometry::detail::robust_policy<boost::geometry::model::d2::point_xy<float>, boost::geometry::model::point<long long int, 2, boost::geometry::cs::cartesian>, float>; Collection = std::vector<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> > >]'  
D:/app/msys64/mingw64/include/boost/geometry/algorithms/union.hpp:325:40:   required from 'static void boost::geometry::resolve_variant::union_<Geometry1, Geometry2>::apply(const Geometry1&, const Geometry2&, Collection&, const Strategy&) [with Collection = std::vector<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> > >; Strategy = boost::geometry::default_strategy; Geometry1 = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >; Geometry2 = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >]'  
D:/app/msys64/mingw64/include/boost/geometry/algorithms/union.hpp:530:17:   required from 'void boost::geometry::union_(const Geometry1&, const Geometry2&, Collection&) [with Geometry1 = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >; Geometry2 = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> >; Collection = std::vector<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<float> > >]'  
D:/dev/project/test/boost-geometry-union-test/main.cpp:14:62:   required from here  
cc1plus.exe: error: this condition has identical branches [-Werror=duplicated-branches]  
cc1plus.exe: all warnings being treated as errors  
ninja: build stopped: subcommand failed.  
14:45:54: The process "D:\app\CMake\bin\cmake.exe" exited with code 1.  
Error while building/deploying project boost-geometry-union-test (kit: Qt 5.10.0 MinGW x64 GCC 7.2)  
When executing step "CMake Build"  
```  
  
Environment: Windows 10 x64, Msys2, GCC 7.2 x64, Boost 1.66.0 x64.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2017-12-28 10:45:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/443#issuecomment-354268803  

Thanks. I cannot reproduce it with GCC 6.2 because it does not know that -Wduplicate-branches yet.  
Neither does clang.   
Will try later to use a newer compiler. For the moment the obvious advice is to remove that directive.
