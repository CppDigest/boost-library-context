# #1304 - sort_by_side.hpp -Wuninitialized at line 299 [Closed]

> Username: ETatara-cadence  
> Created at: 2024-08-20 18:49:58 UTC  
> Updated at: 2024-11-15 21:31:45 UTC  
> Closed at: 2024-11-15 21:31:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/1304  

This message is to report a linux compile warning/error in boost-1.81.0/geometry/algorithms/detail/overlay/sort_by_side.hpp.  gcc shows -Wuninitialized at line 299, and is converted to a hard compile-error with our local usage of -Werror=uninitialized.  
  
Investigation shows this issue may be known as "workaround for VS2015", and perhaps was not fixed for all compilers:  
  
     /// \constructor_default_no_init  
      constexpr point()  
  // Workaround for VS2015  
  #if defined(_MSC_VER) && (_MSC_VER < 1910)  
          : m_values() {}  
  #else  
          = default;  
  #endif  
  
Passing along if this could be fixed in the source.  Thanks.  Our compile log follows:  
  
==========================================  
  ENTERING: be/source/libifrbase/work  
      ../../../../../include/TPtools/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp:299:15: error: 'point.boost::geometry::model::d2::point_xy<double>::<anonymous>.boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>::m_values[0]' is used uninitialized [-Werror=uninitialized]  
	                 from ../../../../../include/TPtools/boost/geometry.hpp:17,  
	                 from ../../../../be/source/libifrbase/include/ifrbbox.h:32,  
	                 from ../../../../be/source/libifrbase/include/ifrshape.h:28,  
	                 from ../../../../be/source/libifrbase/include/ifrdesign.h:26,  
	                 from ../src/ifrdesign.cpp:24:  
	../../../../../include/TPtools/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp: In member function 'Point boost::geometry::detail::overlay::sort_by_side::side_sorter<Reverse1, Reverse2, OverlayType, Point, SideStrategy, Compare>::add(const Turn&, const Operation&, boost::geometry::signed_size_type, int, const Geometry1&, const Geometry2&, bool) [with Turn = boost::geometry::detail::overlay::traversal_turn_info<boost::geometry::model::d2::point_xy<double>, boost::geometry::segment_ratio<double> >; Operation = boost::geometry::detail::overlay::traversal_turn_operation<boost::geometry::model::d2::point_xy<double>, boost::geometry::segment_ratio<double> >; Geometry1 = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double> >; Geometry2 = boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double> >; bool Reverse1 = false; bool Reverse2 = true; boost::geometry::overlay_type OverlayType = boost::geometry::overlay_difference; Point = boost::geometry::model::d2::point_xy<double>; SideStrategy = boost::geometry::strategy::side::side_by_triangle<void>; Compare = std::less<int>]':  
	../../../../../include/TPtools/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp:299:15: error: 'point.boost::geometry::model::d2::point_xy<double>::<anonymous>.boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>::m_values[0]' is used uninitialized [-Werror=uninitialized]  
      ../../../../../include/TPtools/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp:299:15: error: 'point.boost::geometry::model::d2::point_xy<double>::<anonymous>.boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>::m_values[1]' is used uninitialized [-Werror=uninitialized]  
      ../../../../../include/TPtools/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp:299:15: error: 'point.boost::geometry::model::d2::point_xy<double>::<anonymous>.boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>::m_values[0]' is used uninitialized [-Werror=uninitialized]  
      ../../../../../include/TPtools/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp:299:15: error: 'point.boost::geometry::model::d2::point_xy<double>::<anonymous>.boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>::m_values[1]' is used uninitialized [-Werror=uninitialized]  
      make[2]: [../lni64/ifrdesign.o] Error 1 (ignored)  
      ../../../../../include/TPtools/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp:299:15: error: 'point.boost::geometry::model::d2::point_xy<double>::<anonymous>.boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>::m_values[0]' is used uninitialized [-Werror=uninitialized]  
      ../../../../../include/TPtools/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp:299:15: error: 'point.boost::geometry::model::d2::point_xy<double>::<anonymous>.boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>::m_values[1]' is used uninitialized [-Werror=uninitialized]  
      ../../../../../include/TPtools/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp:299:15: error: 'point.boost::geometry::model::d2::point_xy<double>::<anonymous>.boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>::m_values[0]' is used uninitialized [-Werror=uninitialized]  
      ../../../../../include/TPtools/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp:299:15: error: 'point.boost::geometry::model::d2::point_xy<double>::<anonymous>.boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>::m_values[1]' is used uninitialized [-Werror=uninitialized]  
      make[2]: [../lni64/ifrshape.o] Error 1 (ignored)

---

## Comment 1

> Username: vfisikop  
> Created at: 2024-09-09 12:56:26 UTC  
> Updated at: 2024-09-09 12:56:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/1304#issuecomment-2338053933  

Thanks for filing this issue. Could you please provide a minimal example of reproducing it?   
Also, have you tested if the issue persist in 1.86?

---

## Comment 2

> Username: barendgehrels  
> Created at: 2024-09-11 06:47:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/1304#issuecomment-2342798203  

The Boost.Geometry point type is not initialized indeed, it was a design decision on purpose.  
However, you can use your own point type and initialize it.  
  
If you then hit an issue like this, please provide indeed a minimal example as asked by @vissarion

---

## Comment 3

> Username: ETatara-cadence  
> Created at: 2024-09-11 10:53:39 UTC  
> Updated at: 2024-11-15 21:29:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/1304#issuecomment-2343307389  

We are aware of the option of using our own point type.  
We posted this issue after we found that the -Wuninitialized compile warning at line 299 in sort_by_side.hpp interferes with our ability to use -Werror=uninitialized.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2024-11-15 21:31:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/1304#issuecomment-2479962758  

Closing as "by design"
