# #575 - Massive failures in extensions tests [Closed]

> Username: Kojoley  
> Created at: 2019-03-20 20:00:55 UTC  
> Updated at: 2019-04-12 13:10:34 UTC  
> Closed at: 2019-04-08 01:50:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/575  

The regression matrix https://www.boost.org/development/tests/develop/developer/geometry-extensions.html  
  
<details><summary>area_ll - missing header `boost/geometry/strategies/spherical/area_huiller.hpp`</summary>  
  
```  
  "clang++-9" -c -x c++ -fvisibility-inlines-hidden -Wno-c99-extensions -std=c++2a -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_NO_AUTO_PTR -DBOOST_TIMER_DYN_LINK=1 -I".." -I"../boost/geometry/extensions/contrib/ttmath" -I"../libs/boost/geometry/extensions/contrib/ttmath" -I"../libs/geometry/test" -o "/var/boost/run/results/boost/bin.v2/libs/geometry/extensions/test/gis/latlong/area_ll.test/clang-linux-9~c++2a/debug/threading-multi/visibility-hidden/area_ll.o" "../libs/geometry/extensions/test/gis/latlong/area_ll.cpp"  
  
In file included from ../libs/geometry/extensions/test/gis/latlong/area_ll.cpp:25:  
../boost/geometry/extensions/gis/geographic/strategies/area_huiller_earth.hpp:14:10: fatal error: 'boost/geometry/strategies/spherical/area_huiller.hpp' file not found  
#include <boost/geometry/strategies/spherical/area_huiller.hpp>  
         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
```  
  
</details>  
  
<details><summary>cross_track - static assertion error</summary>  
  
```  
  "clang++-9" -c -x c++ -fvisibility-inlines-hidden -Wno-c99-extensions -std=c++2a -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_NO_AUTO_PTR -DBOOST_TIMER_DYN_LINK=1 -I".." -I"../boost/geometry/extensions/contrib/ttmath" -I"../libs/boost/geometry/extensions/contrib/ttmath" -I"../libs/geometry/test" -o "/var/boost/run/results/boost/bin.v2/libs/geometry/extensions/test/gis/latlong/cross_track.test/clang-linux-9~c++2a/debug/threading-multi/visibility-hidden/cross_track.o" "../libs/geometry/extensions/test/gis/latlong/cross_track.cpp"  
  
In file included from ../libs/geometry/extensions/test/gis/latlong/cross_track.cpp:19:  
In file included from ../boost/geometry/algorithms/distance.hpp:23:  
In file included from ../boost/geometry/algorithms/detail/distance/interface.hpp:36:  
../boost/geometry/strategies/distance.hpp:97:5: error: no matching function for call to 'assertion_failed'  
    BOOST_MPL_ASSERT_MSG  
    ^~~~~~~~~~~~~~~~~~~~  
../boost/mpl/assert.hpp:454:51: note: expanded from macro 'BOOST_MPL_ASSERT_MSG'  
#   define BOOST_MPL_ASSERT_MSG( c, msg, types_ ) \  
                                                  ^  
../boost/mpl/assert.hpp:440:9: note: expanded from macro '\  
BOOST_MPL_ASSERT_MSG_IMPL'  
        boost::mpl::assertion_failed<(c)>( BOOST_PP_CAT(mpl_assert_arg,counter)::assert_arg() ) \  
        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../boost/mpl/assert.hpp:60:58: note: expanded from macro '\  
BOOST_MPL_AUX_ASSERT_CONSTANT'  
#   define BOOST_MPL_AUX_ASSERT_CONSTANT(T, expr) enum { expr }  
                                                         ^~~~  
../libs/geometry/extensions/test/gis/latlong/cross_track.cpp:43:56: note: in instantiation of template class 'boost::geometry::strategy::distance::services::default_strategy<boost::geometry::segment_tag, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::geometry::geographic_tag, boost::geometry::geographic_tag, void>' requested here  
    typedef typename bg::strategy::distance::services::default_strategy  
                                                       ^  
../libs/geometry/extensions/test/gis/latlong/cross_track.cpp:86:5: note: in instantiation of function template specialization 'test_distance<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> > >' requested here  
    test_distance<Point>(2, 48, 4, 52, 2, 41, average_earth_radius, p_to_ab, 0.1);  
    ^  
../libs/geometry/extensions/test/gis/latlong/cross_track.cpp:121:5: note: in instantiation of function template specialization 'test_all<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> > >' requested here  
    test_all<bg::model::point<double, 2, bg::cs::geographic<bg::degree> > >();  
    ^  
../boost/mpl/assert.hpp:83:5: note: candidate function template not viable: no known conversion from 'boost::mpl::failed ************(boost::geometry::strategy::distance::services::default_strategy<boost::geometry::segment_tag, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::geometry::geographic_tag, boost::geometry::geographic_tag, void>::NOT_IMPLEMENTED_FOR_THIS_POINT_TYPE_COMBINATION::************)(types<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::geometry::geographic_tag, boost::geometry::geographic_tag>)' to 'typename assert<false>::type' (aka 'mpl_::assert<false>') for 1st argument  
int assertion_failed( typename assert<C>::type );  
    ^  
In file included from ../libs/geometry/extensions/test/gis/latlong/cross_track.cpp:19:  
In file included from ../boost/geometry/algorithms/distance.hpp:23:  
In file included from ../boost/geometry/algorithms/detail/distance/interface.hpp:36:  
../boost/geometry/strategies/distance.hpp:42:5: error: no matching function for call to 'assertion_failed'  
    BOOST_MPL_ASSERT_MSG  
    ^~~~~~~~~~~~~~~~~~~~  
../boost/mpl/assert.hpp:454:51: note: expanded from macro 'BOOST_MPL_ASSERT_MSG'  
#   define BOOST_MPL_ASSERT_MSG( c, msg, types_ ) \  
                                                  ^  
../boost/mpl/assert.hpp:440:9: note: expanded from macro '\  
BOOST_MPL_ASSERT_MSG_IMPL'  
        boost::mpl::assertion_failed<(c)>( BOOST_PP_CAT(mpl_assert_arg,counter)::assert_arg() ) \  
        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../boost/mpl/assert.hpp:60:58: note: expanded from macro '\  
BOOST_MPL_AUX_ASSERT_CONSTANT'  
#   define BOOST_MPL_AUX_ASSERT_CONSTANT(T, expr) enum { expr }  
                                                         ^~~~  
../libs/geometry/extensions/test/gis/latlong/cross_track.cpp:48:56: note: in instantiation of template class 'boost::geometry::strategy::distance::services::return_type<int, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> > >' requested here  
    typedef typename bg::strategy::distance::services::return_type  
                                                       ^  
../libs/geometry/extensions/test/gis/latlong/cross_track.cpp:86:5: note: in instantiation of function template specialization 'test_distance<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> > >' requested here  
    test_distance<Point>(2, 48, 4, 52, 2, 41, average_earth_radius, p_to_ab, 0.1);  
    ^  
../libs/geometry/extensions/test/gis/latlong/cross_track.cpp:121:5: note: in instantiation of function template specialization 'test_all<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> > >' requested here  
    test_all<bg::model::point<double, 2, bg::cs::geographic<bg::degree> > >();  
    ^  
../boost/mpl/assert.hpp:83:5: note: candidate function template not viable: no known conversion from 'boost::mpl::failed ************(boost::geometry::strategy::distance::services::return_type<int, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> > >::NOT_IMPLEMENTED_FOR_THIS_STRATEGY::************)(types<int, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> > >)' to 'typename assert<false>::type' (aka 'mpl_::assert<false>') for 1st argument  
int assertion_failed( typename assert<C>::type );  
    ^  
../libs/geometry/extensions/test/gis/latlong/cross_track.cpp:67:29: error: member reference base type 'strategy_type' (aka 'int') is not a structure or union  
    return_type d = strategy.apply(p1, p2, p3);  
                    ~~~~~~~~^~~~~~  
../libs/geometry/extensions/test/gis/latlong/cross_track.cpp:86:5: note: in instantiation of function template specialization 'test_distance<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> > >' requested here  
    test_distance<Point>(2, 48, 4, 52, 2, 41, average_earth_radius, p_to_ab, 0.1);  
    ^  
../libs/geometry/extensions/test/gis/latlong/cross_track.cpp:121:5: note: in instantiation of function template specialization 'test_all<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> > >' requested here  
    test_all<bg::model::point<double, 2, bg::cs::geographic<bg::degree> > >();  
    ^  
In file included from ../libs/geometry/extensions/test/gis/latlong/cross_track.cpp:22:  
In file included from ../boost/geometry/strategies/spherical/distance_cross_track.hpp:34:  
../boost/geometry/strategies/concepts/distance_concept.hpp:205:25: error: type 'int' cannot be used prior to '::' because it has no members  
        checker::apply(&Strategy::template apply<Point, PointOfSegment>);  
                        ^  
../boost/concept/usage.hpp:16:43: note: in instantiation of member function 'boost::geometry::concepts::PointSegmentDistanceStrategy<int, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> > >::~PointSegmentDistanceStrategy' requested here  
    ~usage_requirements() { ((Model*)0)->~Model(); }  
                                          ^  
../boost/concept/detail/general.hpp:39:42: note: in instantiation of member function 'boost::concepts::usage_requirements<boost::geometry::concepts::PointSegmentDistanceStrategy<int, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> > > >::~usage_requirements' requested here  
    static void failed() { ((Model*)0)->~Model(); }  
                                         ^  
../boost/geometry/strategies/concepts/distance_concept.hpp:203:5: note: in instantiation of member function 'boost::concepts::requirement<boost::concepts::failed ************boost::concepts::usage_requirements<boost::geometry::concepts::PointSegmentDistanceStrategy<int, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> > > >::************>::failed' requested here  
    BOOST_CONCEPT_USAGE(PointSegmentDistanceStrategy)  
    ^  
../boost/concept/usage.hpp:29:7: note: expanded from macro 'BOOST_CONCEPT_USAGE'  
      BOOST_CONCEPT_ASSERT((boost::concepts::usage_requirements<model>)); \  
      ^  
../boost/concept/assert.hpp:43:5: note: expanded from macro 'BOOST_CONCEPT_ASSERT'  
    BOOST_CONCEPT_ASSERT_FN(void(*)ModelInParens)  
    ^  
../boost/concept/detail/general.hpp:71:51: note: expanded from macro 'BOOST_CONCEPT_ASSERT_FN'  
    &::boost::concepts::requirement_<ModelFnPtr>::failed>    \  
                                                  ^  
../libs/geometry/extensions/test/gis/latlong/cross_track.cpp:86:5: note: in instantiation of function template specialization 'test_distance<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> > >' requested here  
    test_distance<Point>(2, 48, 4, 52, 2, 41, average_earth_radius, p_to_ab, 0.1);  
    ^  
../libs/geometry/extensions/test/gis/latlong/cross_track.cpp:121:5: note: in instantiation of function template specialization 'test_all<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> > >' requested here  
    test_all<bg::model::point<double, 2, bg::cs::geographic<bg::degree> > >();  
    ^  
4 errors generated.  
```  
  
</details>  
  
<details><summary>dissolve</summary>  
  
```  
"clang++-9" -c -x c++ -fvisibility-inlines-hidden -Wno-c99-extensions -std=c++2a -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_NO_AUTO_PTR -DBOOST_TIMER_DYN_LINK=1 -I".." -I"../boost/geometry/extensions/contrib/ttmath" -I"../libs/boost/geometry/extensions/contrib/ttmath" -I"../libs/geometry/test" -o "/var/boost/run/results/boost/bin.v2/libs/geometry/extensions/test/algorithms/dissolve.test/clang-linux-9~c++2a/debug/threading-multi/visibility-hidden/dissolve.o" "../libs/geometry/extensions/test/algorithms/dissolve.cpp"  
  
../libs/geometry/extensions/test/algorithms/dissolve.cpp:579:43: warning: unused typedef 'ring' [-Wunused-local-typedef]  
    typedef bg::model::ring<P, Clockwise> ring;  
                                          ^  
1 warning generated.  
              
Link [2019-03-17 12:49:51 UTC]: succeed  
  
"clang++-9"  -Wl,-R -Wl,"/var/boost/run/results/boost/bin.v2/libs/chrono/build/clang-linux-9~c++2a/debug/threading-multi/visibility-hidden" -Wl,-R -Wl,"/var/boost/run/results/boost/bin.v2/libs/timer/build/clang-linux-9~c++2a/debug/threading-multi/visibility-hidden" -Wl,-rpath-link -Wl,"/var/boost/run/results/boost/bin.v2/libs/chrono/build/clang-linux-9~c++2a/debug/threading-multi/visibility-hidden" -Wl,-rpath-link -Wl,"/var/boost/run/results/boost/bin.v2/libs/timer/build/clang-linux-9~c++2a/debug/threading-multi/visibility-hidden" -o "/var/boost/run/results/boost/bin.v2/libs/geometry/extensions/test/algorithms/dissolve.test/clang-linux-9~c++2a/debug/threading-multi/visibility-hidden/dissolve" -Wl,--start-group "/var/boost/run/results/boost/bin.v2/libs/geometry/extensions/test/algorithms/dissolve.test/clang-linux-9~c++2a/debug/threading-multi/visibility-hidden/dissolve.o" "/var/boost/run/results/boost/bin.v2/libs/timer/build/clang-linux-9~c++2a/debug/threading-multi/visibility-hidden/libboost_timer.so.1.70.0"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -m64 -pthread -g -fvisibility=hidden -fvisibility-inlines-hidden   
  
              
Run [2019-03-17 12:49:51 UTC]: fail  
  
Running 1 test case...  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(427): ?[1;31;49merror: in "test_main_caller( argc, argv )": dissolve: gitter_2013_04_a geometry is not valid: Geometry has invalid self-intersections. A self-intersection point was found at (31.3992, 28.9749); method: t; operations: i/u; segment IDs {source, multi, ring, segment}: {0, 1, -1, 4}/{0, 1, 0, 1}?[0;39;49m  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(454): ?[1;31;49merror: in "test_main_caller( argc, argv )": dissolve: gitter_2013_04_a #points expected: 21 detected: 27 type: f?[0;39;49m  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(463): ?[1;31;49merror: in "test_main_caller( argc, argv )": check holes == expected_hole_count has failed [1 != 0]?[0;39;49m  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(464): ?[1;31;49merror: in "test_main_caller( argc, argv )": difference{0.199697} between length_or_area{2537.2387935761913} and expected_area{3043.9180999999999} exceeds 0.001%?[0;39;49m  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(427): ?[1;31;49merror: in "test_main_caller( argc, argv )": dissolve: gitter_2013_04_a_rev geometry is not valid: Geometry has invalid self-intersections. A self-intersection point was found at (31.3992, 28.9749); method: t; operations: i/u; segment IDs {source, multi, ring, segment}: {0, 1, -1, 4}/{0, 1, 0, 1}?[0;39;49m  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(454): ?[1;31;49merror: in "test_main_caller( argc, argv )": dissolve: gitter_2013_04_a_rev #points expected: 21 detected: 27 type: f?[0;39;49m  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(463): ?[1;31;49merror: in "test_main_caller( argc, argv )": check holes == expected_hole_count has failed [1 != 0]?[0;39;49m  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(464): ?[1;31;49merror: in "test_main_caller( argc, argv )": difference{0.199697} between length_or_area{2537.2387935761913} and expected_area{3043.9180999999999} exceeds 0.001%?[0;39;49m  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(427): ?[1;31;49merror: in "test_main_caller( argc, argv )": dissolve: gitter_2013_04_a geometry is not valid: Geometry has invalid self-intersections. A self-intersection point was found at (31.3992, 28.9749); method: t; operations: i/u; segment IDs {source, multi, ring, segment}: {0, 1, -1, 4}/{0, 1, 0, 1}?[0;39;49m  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(454): ?[1;31;49merror: in "test_main_caller( argc, argv )": dissolve: gitter_2013_04_a #points expected: 21 detected: 27 type: d?[0;39;49m  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(463): ?[1;31;49merror: in "test_main_caller( argc, argv )": check holes == expected_hole_count has failed [1 != 0]?[0;39;49m  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(464): ?[1;31;49merror: in "test_main_caller( argc, argv )": difference{0.199697} between length_or_area{2537.238491342312} and expected_area{3043.9180999999999} exceeds 0.001%?[0;39;49m  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(427): ?[1;31;49merror: in "test_main_caller( argc, argv )": dissolve: gitter_2013_04_a_rev geometry is not valid: Geometry has invalid self-intersections. A self-intersection point was found at (31.3992, 28.9749); method: t; operations: i/u; segment IDs {source, multi, ring, segment}: {0, 1, -1, 4}/{0, 1, 0, 1}?[0;39;49m  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(454): ?[1;31;49merror: in "test_main_caller( argc, argv )": dissolve: gitter_2013_04_a_rev #points expected: 21 detected: 27 type: d?[0;39;49m  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(463): ?[1;31;49merror: in "test_main_caller( argc, argv )": check holes == expected_hole_count has failed [1 != 0]?[0;39;49m  
../libs/geometry/extensions/test/algorithms/dissolve.cpp(464): ?[1;31;49merror: in "test_main_caller( argc, argv )": difference{0.199697} between length_or_area{2537.238491342312} and expected_area{3043.9180999999999} exceeds 0.001%?[0;39;49m  
  
?[1;31;49m*** 16 failures are detected in the test module "Test Program"  
?[0;39;49m  
EXIT STATUS: 201  
```  
  
</details>  
  
<details><summary>distance_mixed - no type named 'type' in 'boost::geometry::core_dispatch::radius_type<void, double>'</summary>  
  
```  
  "clang++-9" -c -x c++ -fvisibility-inlines-hidden -Wno-c99-extensions -std=c++2a -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_NO_AUTO_PTR -DBOOST_TIMER_DYN_LINK=1 -I".." -I"../boost/geometry/extensions/contrib/ttmath" -I"../libs/boost/geometry/extensions/contrib/ttmath" -I"../libs/geometry/test" -o "/var/boost/run/results/boost/bin.v2/libs/geometry/extensions/test/gis/latlong/distance_mixed.test/clang-linux-9~c++2a/debug/threading-multi/visibility-hidden/distance_mixed.o" "../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp"  
  
In file included from ../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:21:  
In file included from ../boost/geometry/geometry.hpp:43:  
../boost/geometry/core/radius.hpp:115:28: error: no type named 'type' in 'boost::geometry::core_dispatch::radius_type<void, double>'  
                        >::type type;  
                        ~~~^~~~  
../boost/geometry/core/radius.hpp:126:17: note: in instantiation of template class 'boost::geometry::radius_type<double>' requested here  
inline typename radius_type<Geometry>::type get_radius(Geometry const& geometry)  
                ^  
../boost/geometry/formulas/meridian_inverse.hpp:115:22: note: while substituting deduced template arguments into function template 'get_radius' [with I = 0, Geometry = double]  
        CT const a = get_radius<0>(spheroid);  
                     ^  
../boost/geometry/formulas/meridian_inverse.hpp:66:26: note: in instantiation of function template specialization 'boost::geometry::formula::meridian_inverse<double, 4>::apply<double, double>' requested here  
        return math::abs(apply(lat2, spheroid) - apply(lat1, spheroid));  
                         ^  
../boost/geometry/formulas/meridian_inverse.hpp:97:28: note: in instantiation of function template specialization 'boost::geometry::formula::meridian_inverse<double, 4>::meridian_not_crossing_pole_dist<double, double>' requested here  
            res.distance = meridian_not_crossing_pole_dist(lat1, lat2, spheroid);  
                           ^  
../boost/geometry/strategies/geographic/distance.hpp:101:36: note: in instantiation of function template specialization 'boost::geometry::formula::meridian_inverse<double, 4>::apply<double, double>' requested here  
                 meridian_inverse::apply(lon1, lat1, lon2, lat2, spheroid);  
                                   ^  
../boost/geometry/strategies/geographic/distance.hpp:125:16: note: (skipping 1 context in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
        return apply(lon1, lat1, lon2, lat2, m_spheroid);  
               ^  
../boost/geometry/algorithms/detail/distance/point_to_geometry.hpp:69:25: note: in instantiation of function template specialization 'boost::geometry::strategy::distance::geographic<boost::geometry::strategy::vincenty, double, void>::apply<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
        return strategy.apply(p1, p2);  
                        ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:110:16: note: in instantiation of member function 'boost::geometry::detail::distance::point_to_point<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void> >::apply' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:155:16: note: in instantiation of function template specialization 'boost::geometry::resolve_strategy::distance<boost::geometry::strategy::distance::vincenty<double, void> >::apply<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:382:19: note: in instantiation of function template specialization 'boost::geometry::resolve_variant::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >::apply<boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
               >::apply(geometry1, geometry2, strategy);  
                  ^  
../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:58:23: note: in instantiation of function template specialization 'boost::geometry::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
    BOOST_CHECK_CLOSE(distance(paris, amsterdam, the_strategy), expected, tolerance);  
                      ^  
In file included from ../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:21:  
In file included from ../boost/geometry/geometry.hpp:49:  
In file included from ../boost/geometry/strategies/strategies.hpp:97:  
In file included from ../boost/geometry/strategies/geographic/distance.hpp:25:  
../boost/geometry/formulas/meridian_inverse.hpp:115:22: error: call to function 'get_radius' that is neither visible in the template definition nor found by argument-dependent lookup  
        CT const a = get_radius<0>(spheroid);  
                     ^  
../boost/geometry/formulas/meridian_inverse.hpp:66:26: note: in instantiation of function template specialization 'boost::geometry::formula::meridian_inverse<double, 4>::apply<double, double>' requested here  
        return math::abs(apply(lat2, spheroid) - apply(lat1, spheroid));  
                         ^  
../boost/geometry/formulas/meridian_inverse.hpp:97:28: note: in instantiation of function template specialization 'boost::geometry::formula::meridian_inverse<double, 4>::meridian_not_crossing_pole_dist<double, double>' requested here  
            res.distance = meridian_not_crossing_pole_dist(lat1, lat2, spheroid);  
                           ^  
../boost/geometry/strategies/geographic/distance.hpp:101:36: note: in instantiation of function template specialization 'boost::geometry::formula::meridian_inverse<double, 4>::apply<double, double>' requested here  
                 meridian_inverse::apply(lon1, lat1, lon2, lat2, spheroid);  
                                   ^  
../boost/geometry/strategies/geographic/distance.hpp:125:16: note: in instantiation of function template specialization 'boost::geometry::strategy::distance::geographic<boost::geometry::strategy::vincenty, double, void>::apply<double>' requested here  
        return apply(lon1, lat1, lon2, lat2, m_spheroid);  
               ^  
../boost/geometry/algorithms/detail/distance/point_to_geometry.hpp:69:25: note: in instantiation of function template specialization 'boost::geometry::strategy::distance::geographic<boost::geometry::strategy::vincenty, double, void>::apply<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
        return strategy.apply(p1, p2);  
                        ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:110:16: note: in instantiation of member function 'boost::geometry::detail::distance::point_to_point<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void> >::apply' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:155:16: note: in instantiation of function template specialization 'boost::geometry::resolve_strategy::distance<boost::geometry::strategy::distance::vincenty<double, void> >::apply<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:382:19: note: in instantiation of function template specialization 'boost::geometry::resolve_variant::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >::apply<boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
               >::apply(geometry1, geometry2, strategy);  
                  ^  
../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:58:23: note: in instantiation of function template specialization 'boost::geometry::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
    BOOST_CHECK_CLOSE(distance(paris, amsterdam, the_strategy), expected, tolerance);  
                      ^  
../boost/geometry/core/radius.hpp:126:45: note: 'get_radius' should be declared prior to the call site  
inline typename radius_type<Geometry>::type get_radius(Geometry const& geometry)  
                                            ^  
../boost/geometry/core/radius.hpp:134:16: error: no member named 'get' in 'boost::geometry::core_dispatch::radius_access<void, double, 0, boost::integral_constant<bool, false> >'  
            >::get(geometry);  
            ~~~^  
../boost/geometry/formulas/meridian_inverse.hpp:115:22: note: in instantiation of function template specialization 'boost::geometry::get_radius<0, double>' requested here  
        CT const a = get_radius<0>(spheroid);  
                     ^  
../boost/geometry/formulas/meridian_inverse.hpp:66:26: note: in instantiation of function template specialization 'boost::geometry::formula::meridian_inverse<double, 4>::apply<double, double>' requested here  
        return math::abs(apply(lat2, spheroid) - apply(lat1, spheroid));  
                         ^  
../boost/geometry/formulas/meridian_inverse.hpp:97:28: note: in instantiation of function template specialization 'boost::geometry::formula::meridian_inverse<double, 4>::meridian_not_crossing_pole_dist<double, double>' requested here  
            res.distance = meridian_not_crossing_pole_dist(lat1, lat2, spheroid);  
                           ^  
../boost/geometry/strategies/geographic/distance.hpp:101:36: note: in instantiation of function template specialization 'boost::geometry::formula::meridian_inverse<double, 4>::apply<double, double>' requested here  
                 meridian_inverse::apply(lon1, lat1, lon2, lat2, spheroid);  
                                   ^  
../boost/geometry/strategies/geographic/distance.hpp:125:16: note: in instantiation of function template specialization 'boost::geometry::strategy::distance::geographic<boost::geometry::strategy::vincenty, double, void>::apply<double>' requested here  
        return apply(lon1, lat1, lon2, lat2, m_spheroid);  
               ^  
../boost/geometry/algorithms/detail/distance/point_to_geometry.hpp:69:25: note: in instantiation of function template specialization 'boost::geometry::strategy::distance::geographic<boost::geometry::strategy::vincenty, double, void>::apply<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
        return strategy.apply(p1, p2);  
                        ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:110:16: note: in instantiation of member function 'boost::geometry::detail::distance::point_to_point<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void> >::apply' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:155:16: note: in instantiation of function template specialization 'boost::geometry::resolve_strategy::distance<boost::geometry::strategy::distance::vincenty<double, void> >::apply<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:382:19: note: in instantiation of function template specialization 'boost::geometry::resolve_variant::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >::apply<boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
               >::apply(geometry1, geometry2, strategy);  
                  ^  
../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:58:23: note: in instantiation of function template specialization 'boost::geometry::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
    BOOST_CHECK_CLOSE(distance(paris, amsterdam, the_strategy), expected, tolerance);  
                      ^  
In file included from ../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:19:  
In file included from ../boost/geometry/algorithms/assign.hpp:32:  
In file included from ../boost/geometry/algorithms/detail/assign_box_corners.hpp:20:  
In file included from ../boost/geometry/geometries/concepts/check.hpp:38:  
../boost/geometry/algorithms/not_implemented.hpp:69:5: error: no matching function for call to 'assertion_failed'  
    BOOST_MPL_ASSERT_MSG  
    ^~~~~~~~~~~~~~~~~~~~  
../boost/mpl/assert.hpp:454:51: note: expanded from macro 'BOOST_MPL_ASSERT_MSG'  
#   define BOOST_MPL_ASSERT_MSG( c, msg, types_ ) \  
                                                  ^  
../boost/mpl/assert.hpp:440:9: note: expanded from macro '\  
BOOST_MPL_ASSERT_MSG_IMPL'  
        boost::mpl::assertion_failed<(c)>( BOOST_PP_CAT(mpl_assert_arg,counter)::assert_arg() ) \  
        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../boost/mpl/assert.hpp:60:58: note: expanded from macro '\  
BOOST_MPL_AUX_ASSERT_CONSTANT'  
#   define BOOST_MPL_AUX_ASSERT_CONSTANT(T, expr) enum { expr }  
                                                         ^~~~  
../boost/geometry/algorithms/not_implemented.hpp:110:7: note: in instantiation of template class 'boost::geometry::nyi::not_implemented_error<void, void, void>' requested here  
      nyi::not_implemented_error  
      ^  
../boost/geometry/formulas/flattening.hpp:29:7: note: in instantiation of template class 'boost::geometry::not_implemented<void, void, void>' requested here  
    : not_implemented<Tag>  
      ^  
../boost/geometry/formulas/flattening.hpp:62:30: note: in instantiation of template class 'boost::geometry::formula_dispatch::flattening<double, double, void>' requested here  
    return formula_dispatch::flattening<ResultType, Geometry>::apply(geometry);  
                             ^  
../boost/geometry/formulas/meridian_inverse.hpp:116:31: note: in instantiation of function template specialization 'boost::geometry::formula::flattening<double, double>' requested here  
        CT const f = formula::flattening<CT>(spheroid);  
                              ^  
../boost/geometry/formulas/meridian_inverse.hpp:66:26: note: in instantiation of function template specialization 'boost::geometry::formula::meridian_inverse<double, 4>::apply<double, double>' requested here  
        return math::abs(apply(lat2, spheroid) - apply(lat1, spheroid));  
                         ^  
../boost/geometry/formulas/meridian_inverse.hpp:97:28: note: (skipping 3 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
            res.distance = meridian_not_crossing_pole_dist(lat1, lat2, spheroid);  
                           ^  
../boost/geometry/algorithms/detail/distance/point_to_geometry.hpp:69:25: note: in instantiation of function template specialization 'boost::geometry::strategy::distance::geographic<boost::geometry::strategy::vincenty, double, void>::apply<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
        return strategy.apply(p1, p2);  
                        ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:110:16: note: in instantiation of member function 'boost::geometry::detail::distance::point_to_point<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void> >::apply' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:155:16: note: in instantiation of function template specialization 'boost::geometry::resolve_strategy::distance<boost::geometry::strategy::distance::vincenty<double, void> >::apply<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:382:19: note: in instantiation of function template specialization 'boost::geometry::resolve_variant::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >::apply<boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
               >::apply(geometry1, geometry2, strategy);  
                  ^  
../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:58:23: note: in instantiation of function template specialization 'boost::geometry::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
    BOOST_CHECK_CLOSE(distance(paris, amsterdam, the_strategy), expected, tolerance);  
                      ^  
../boost/mpl/assert.hpp:83:5: note: candidate function template not viable: no known conversion from 'boost::mpl::failed ************(boost::geometry::nyi::not_implemented_error<void, void, void>::THIS_OPERATION_IS_NOT_OR_NOT_YET_IMPLEMENTED::************)(types<void, void, void>)' to 'typename assert<false>::type' (aka 'mpl_::assert<false>') for 1st argument  
int assertion_failed( typename assert<C>::type );  
    ^  
In file included from ../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:21:  
In file included from ../boost/geometry/geometry.hpp:49:  
In file included from ../boost/geometry/strategies/strategies.hpp:39:  
In file included from ../boost/geometry/strategies/intersection_strategies.hpp:30:  
In file included from ../boost/geometry/strategies/spherical/intersection.hpp:46:  
In file included from ../boost/geometry/strategies/spherical/envelope.hpp:32:  
In file included from ../boost/geometry/strategies/spherical/expand_segment.hpp:32:  
In file included from ../boost/geometry/algorithms/detail/envelope/segment.hpp:31:  
In file included from ../boost/geometry/strategies/geographic/envelope_segment.hpp:19:  
In file included from ../boost/geometry/strategies/geographic/azimuth.hpp:18:  
In file included from ../boost/geometry/strategies/geographic/parameters.hpp:17:  
../boost/geometry/formulas/vincenty_inverse.hpp:100:32: error: no matching function for call to 'get_radius'  
        CT const radius_a = CT(get_radius<0>(spheroid));  
                               ^~~~~~~~~~~~~  
../boost/geometry/strategies/geographic/distance.hpp:111:20: note: in instantiation of function template specialization 'boost::geometry::formula::vincenty_inverse<double, true, false, false, false, false>::apply<double, double, double>' requested here  
                >::apply(lon1, lat1, lon2, lat2, spheroid).distance;  
                   ^  
../boost/geometry/strategies/geographic/distance.hpp:125:16: note: in instantiation of function template specialization 'boost::geometry::strategy::distance::geographic<boost::geometry::strategy::vincenty, double, void>::apply<double>' requested here  
        return apply(lon1, lat1, lon2, lat2, m_spheroid);  
               ^  
../boost/geometry/algorithms/detail/distance/point_to_geometry.hpp:69:25: note: in instantiation of function template specialization 'boost::geometry::strategy::distance::geographic<boost::geometry::strategy::vincenty, double, void>::apply<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
        return strategy.apply(p1, p2);  
                        ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:110:16: note: in instantiation of member function 'boost::geometry::detail::distance::point_to_point<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void> >::apply' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:155:16: note: in instantiation of function template specialization 'boost::geometry::resolve_strategy::distance<boost::geometry::strategy::distance::vincenty<double, void> >::apply<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:382:19: note: in instantiation of function template specialization 'boost::geometry::resolve_variant::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >::apply<boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
               >::apply(geometry1, geometry2, strategy);  
                  ^  
../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:58:23: note: in instantiation of function template specialization 'boost::geometry::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
    BOOST_CHECK_CLOSE(distance(paris, amsterdam, the_strategy), expected, tolerance);  
                      ^  
../boost/geometry/core/radius.hpp:126:45: note: candidate template ignored: substitution failure [with I = 0, Geometry = double]  
inline typename radius_type<Geometry>::type get_radius(Geometry const& geometry)  
                                            ^  
In file included from ../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:21:  
In file included from ../boost/geometry/geometry.hpp:49:  
In file included from ../boost/geometry/strategies/strategies.hpp:39:  
In file included from ../boost/geometry/strategies/intersection_strategies.hpp:30:  
In file included from ../boost/geometry/strategies/spherical/intersection.hpp:46:  
In file included from ../boost/geometry/strategies/spherical/envelope.hpp:32:  
In file included from ../boost/geometry/strategies/spherical/expand_segment.hpp:32:  
In file included from ../boost/geometry/algorithms/detail/envelope/segment.hpp:31:  
In file included from ../boost/geometry/strategies/geographic/envelope_segment.hpp:19:  
In file included from ../boost/geometry/strategies/geographic/azimuth.hpp:18:  
In file included from ../boost/geometry/strategies/geographic/parameters.hpp:17:  
../boost/geometry/formulas/vincenty_inverse.hpp:102:22: error: no matching function for call to 'flattening'  
        CT const f = formula::flattening<CT>(spheroid);  
                     ^~~~~~~~~~~~~~~~~~~~~~~  
../boost/geometry/formulas/flattening.hpp:60:12: note: candidate template ignored: substitution failure [with ResultType = double, Geometry = double]  
ResultType flattening(Geometry const& geometry)  
           ^  
In file included from ../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:21:  
In file included from ../boost/geometry/geometry.hpp:43:  
../boost/geometry/core/radius.hpp:134:16: error: no member named 'get' in 'boost::geometry::core_dispatch::radius_access<void, double, 2, boost::integral_constant<bool, false> >'  
            >::get(geometry);  
            ~~~^  
../boost/geometry/formulas/vincenty_inverse.hpp:101:32: note: in instantiation of function template specialization 'boost::geometry::get_radius<2, double>' requested here  
        CT const radius_b = CT(get_radius<2>(spheroid));  
                               ^  
../boost/geometry/strategies/geographic/distance.hpp:111:20: note: in instantiation of function template specialization 'boost::geometry::formula::vincenty_inverse<double, true, false, false, false, false>::apply<double, double, double>' requested here  
                >::apply(lon1, lat1, lon2, lat2, spheroid).distance;  
                   ^  
../boost/geometry/strategies/geographic/distance.hpp:125:16: note: in instantiation of function template specialization 'boost::geometry::strategy::distance::geographic<boost::geometry::strategy::vincenty, double, void>::apply<double>' requested here  
        return apply(lon1, lat1, lon2, lat2, m_spheroid);  
               ^  
../boost/geometry/algorithms/detail/distance/point_to_geometry.hpp:69:25: note: in instantiation of function template specialization 'boost::geometry::strategy::distance::geographic<boost::geometry::strategy::vincenty, double, void>::apply<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
        return strategy.apply(p1, p2);  
                        ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:110:16: note: in instantiation of member function 'boost::geometry::detail::distance::point_to_point<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void> >::apply' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:155:16: note: in instantiation of function template specialization 'boost::geometry::resolve_strategy::distance<boost::geometry::strategy::distance::vincenty<double, void> >::apply<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:382:19: note: in instantiation of function template specialization 'boost::geometry::resolve_variant::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >::apply<boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
               >::apply(geometry1, geometry2, strategy);  
                  ^  
../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:58:23: note: in instantiation of function template specialization 'boost::geometry::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
    BOOST_CHECK_CLOSE(distance(paris, amsterdam, the_strategy), expected, tolerance);  
                      ^  
In file included from ../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:21:  
In file included from ../boost/geometry/geometry.hpp:49:  
In file included from ../boost/geometry/strategies/strategies.hpp:81:  
../boost/geometry/strategies/spherical/distance_cross_track.hpp:529:32: error: no type named 'radius_type' in 'boost::geometry::strategy::distance::vincenty<double, void>'  
    typedef typename Strategy::radius_type radius_type;  
            ~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~  
../boost/geometry/strategies/spherical/distance_cross_track.hpp:603:7: note: in instantiation of template class 'boost::geometry::strategy::distance::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
    : cross_track<CalculationType, Strategy>::template return_type<P, PS>  
      ^  
../boost/geometry/algorithms/detail/distance/point_to_geometry.hpp:95:52: note: in instantiation of template class 'boost::geometry::strategy::distance::services::return_type<boost::geometry::strategy::distance::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> >, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
    typedef typename strategy::distance::services::return_type  
                                                   ^  
../boost/geometry/algorithms/detail/distance/point_to_geometry.hpp:377:9: note: in instantiation of template class 'boost::geometry::detail::distance::point_to_range<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::closed, boost::geometry::strategy::distance::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> > >' requested here  
    > : detail::distance::point_to_range<Point, Linestring, closed, Strategy>  
        ^  
../boost/geometry/algorithms/detail/distance/point_to_geometry.hpp:492:9: note: in instantiation of template class 'boost::geometry::dispatch::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::strategy::distance::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> >, boost::geometry::point_tag, boost::geometry::linestring_tag, boost::geometry::strategy_tag_distance_point_segment, false>' requested here  
    > : distance  
        ^  
../boost/geometry/algorithms/detail/distance/backward_compatibility.hpp:232:16: note: in instantiation of template class 'boost::geometry::dispatch::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::strategy::distance::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> >, boost::geometry::point_tag, boost::geometry::linear_tag, boost::geometry::strategy_tag_distance_point_segment, false>' requested here  
        return distance  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:110:16: note: in instantiation of member function 'boost::geometry::dispatch::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::strategy::distance::vincenty<double, void>, boost::geometry::point_tag, boost::geometry::linear_tag, boost::geometry::strategy_tag_distance_point_point, false>::apply' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:155:16: note: in instantiation of function template specialization 'boost::geometry::resolve_strategy::distance<boost::geometry::strategy::distance::vincenty<double, void> >::apply<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator> >' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:382:19: note: in instantiation of function template specialization 'boost::geometry::resolve_variant::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator> >::apply<boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
               >::apply(geometry1, geometry2, strategy);  
                  ^  
../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:79:23: note: in instantiation of function template specialization 'boost::geometry::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
    BOOST_CHECK_CLOSE(distance(paris, ab, the_strategy), expected, tolerance);  
                      ^  
In file included from ../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:21:  
In file included from ../boost/geometry/geometry.hpp:49:  
In file included from ../boost/geometry/strategies/strategies.hpp:81:  
../boost/geometry/strategies/spherical/distance_cross_track.hpp:534:52: error: no type named 'radius_type' in 'boost::geometry::strategy::distance::vincenty<double, void>'  
    explicit inline cross_track(typename Strategy::radius_type const& r)  
                                ~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~  
../boost/geometry/strategies/spherical/distance_cross_track.hpp:580:31: error: no type named 'radius_type' in 'boost::geometry::strategy::distance::vincenty<double, void>'  
    inline typename Strategy::radius_type radius() const  
           ~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~  
../boost/geometry/strategies/spherical/distance_cross_track.hpp:603:56: error: no member named 'return_type' in 'boost::geometry::strategy::distance::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> >'  
    : cross_track<CalculationType, Strategy>::template return_type<P, PS>  
      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~         ^  
../boost/geometry/algorithms/detail/distance/point_to_geometry.hpp:95:52: note: in instantiation of template class 'boost::geometry::strategy::distance::services::return_type<boost::geometry::strategy::distance::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> >, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
    typedef typename strategy::distance::services::return_type  
                                                   ^  
../boost/geometry/algorithms/detail/distance/point_to_geometry.hpp:377:9: note: in instantiation of template class 'boost::geometry::detail::distance::point_to_range<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::closed, boost::geometry::strategy::distance::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> > >' requested here  
    > : detail::distance::point_to_range<Point, Linestring, closed, Strategy>  
        ^  
../boost/geometry/algorithms/detail/distance/point_to_geometry.hpp:492:9: note: in instantiation of template class 'boost::geometry::dispatch::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::strategy::distance::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> >, boost::geometry::point_tag, boost::geometry::linestring_tag, boost::geometry::strategy_tag_distance_point_segment, false>' requested here  
    > : distance  
        ^  
../boost/geometry/algorithms/detail/distance/backward_compatibility.hpp:232:16: note: in instantiation of template class 'boost::geometry::dispatch::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::strategy::distance::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> >, boost::geometry::point_tag, boost::geometry::linear_tag, boost::geometry::strategy_tag_distance_point_segment, false>' requested here  
        return distance  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:110:16: note: in instantiation of member function 'boost::geometry::dispatch::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::strategy::distance::vincenty<double, void>, boost::geometry::point_tag, boost::geometry::linear_tag, boost::geometry::strategy_tag_distance_point_point, false>::apply' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:155:16: note: in instantiation of function template specialization 'boost::geometry::resolve_strategy::distance<boost::geometry::strategy::distance::vincenty<double, void> >::apply<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator> >' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:382:19: note: in instantiation of function template specialization 'boost::geometry::resolve_variant::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator> >::apply<boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
               >::apply(geometry1, geometry2, strategy);  
                  ^  
../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:79:23: note: in instantiation of function template specialization 'boost::geometry::distance<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
    BOOST_CHECK_CLOSE(distance(paris, ab, the_strategy), expected, tolerance);  
                      ^  
In file included from ../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:21:  
In file included from ../boost/geometry/geometry.hpp:49:  
In file included from ../boost/geometry/strategies/strategies.hpp:81:  
../boost/geometry/strategies/spherical/distance_cross_track.hpp:348:32: error: no type named 'radius_type' in 'boost::geometry::strategy::distance::vincenty<double, void>'  
    typedef typename Strategy::radius_type radius_type;  
            ~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~  
../boost/geometry/strategies/spherical/distance_cross_track.hpp:677:19: note: in instantiation of template class 'boost::geometry::strategy::distance::comparable::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
    : comparable::cross_track  
                  ^  
../boost/geometry/algorithms/detail/distance/point_to_geometry.hpp:114:23: note: in instantiation of template class 'boost::geometry::strategy::distance::services::return_type<boost::geometry::strategy::distance::comparable::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> >, boost::geometry::model::ll::point<boost::geometry::radian, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
        typename sds::return_type  
                      ^  
../boost/geometry/algorithms/detail/distance/backward_compatibility.hpp:237:16: note: in instantiation of member function 'boost::geometry::detail::distance::point_to_range<boost::geometry::model::ll::point<boost::geometry::radian, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::closed, boost::geometry::strategy::distance::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> > >::apply' requested here  
            >::apply(point, multigeometry, ps_strategy_type());  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:86:16: note: in instantiation of member function 'boost::geometry::dispatch::distance<boost::geometry::model::ll::point<boost::geometry::radian, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::strategy::distance::vincenty<double, void>, boost::geometry::point_tag, boost::geometry::linear_tag, boost::geometry::strategy_tag_distance_point_point, false>::apply' requested here  
            >::apply(g2, g1, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:110:16: note: in instantiation of member function 'boost::geometry::dispatch::distance<boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::model::ll::point<boost::geometry::radian, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void>, boost::geometry::linear_tag, boost::geometry::point_tag, boost::geometry::strategy_tag_distance_point_point, true>::apply' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:155:16: note: in instantiation of function template specialization 'boost::geometry::resolve_strategy::distance<boost::geometry::strategy::distance::vincenty<double, void> >::apply<boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::model::ll::point<boost::geometry::radian, double, cs::geographic, 2> >' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:382:19: note: in instantiation of function template specialization 'boost::geometry::resolve_variant::distance<boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::model::ll::point<boost::geometry::radian, double, cs::geographic, 2> >::apply<boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
               >::apply(geometry1, geometry2, strategy);  
                  ^  
../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:83:23: note: in instantiation of function template specialization 'boost::geometry::distance<boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::model::ll::point<boost::geometry::radian, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
    BOOST_CHECK_CLOSE(distance(ab, paris_rad, the_strategy), expected, tolerance);  
                      ^  
In file included from ../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:21:  
In file included from ../boost/geometry/geometry.hpp:49:  
In file included from ../boost/geometry/strategies/strategies.hpp:81:  
../boost/geometry/strategies/spherical/distance_cross_track.hpp:353:52: error: no type named 'radius_type' in 'boost::geometry::strategy::distance::vincenty<double, void>'  
    explicit inline cross_track(typename Strategy::radius_type const& r)  
                                ~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~  
../boost/geometry/strategies/spherical/distance_cross_track.hpp:482:31: error: no type named 'radius_type' in 'boost::geometry::strategy::distance::vincenty<double, void>'  
    inline typename Strategy::radius_type radius() const  
           ~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~  
../boost/geometry/strategies/spherical/distance_cross_track.hpp:680:21: error: no member named 'return_type' in 'boost::geometry::strategy::distance::comparable::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> >'  
        >::template return_type<P, PS>  
        ~~~         ^  
../boost/geometry/algorithms/detail/distance/point_to_geometry.hpp:114:23: note: in instantiation of template class 'boost::geometry::strategy::distance::services::return_type<boost::geometry::strategy::distance::comparable::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> >, boost::geometry::model::ll::point<boost::geometry::radian, double, cs::geographic, 2>, boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2> >' requested here  
        typename sds::return_type  
                      ^  
../boost/geometry/algorithms/detail/distance/backward_compatibility.hpp:237:16: note: in instantiation of member function 'boost::geometry::detail::distance::point_to_range<boost::geometry::model::ll::point<boost::geometry::radian, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::closed, boost::geometry::strategy::distance::cross_track<void, boost::geometry::strategy::distance::vincenty<double, void> > >::apply' requested here  
            >::apply(point, multigeometry, ps_strategy_type());  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:86:16: note: in instantiation of member function 'boost::geometry::dispatch::distance<boost::geometry::model::ll::point<boost::geometry::radian, double, cs::geographic, 2>, boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::strategy::distance::vincenty<double, void>, boost::geometry::point_tag, boost::geometry::linear_tag, boost::geometry::strategy_tag_distance_point_point, false>::apply' requested here  
            >::apply(g2, g1, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:110:16: note: in instantiation of member function 'boost::geometry::dispatch::distance<boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::model::ll::point<boost::geometry::radian, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void>, boost::geometry::linear_tag, boost::geometry::point_tag, boost::geometry::strategy_tag_distance_point_point, true>::apply' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:155:16: note: in instantiation of function template specialization 'boost::geometry::resolve_strategy::distance<boost::geometry::strategy::distance::vincenty<double, void> >::apply<boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::model::ll::point<boost::geometry::radian, double, cs::geographic, 2> >' requested here  
            >::apply(geometry1, geometry2, strategy);  
               ^  
../boost/geometry/algorithms/detail/distance/interface.hpp:382:19: note: in instantiation of function template specialization 'boost::geometry::resolve_variant::distance<boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::model::ll::point<boost::geometry::radian, double, cs::geographic, 2> >::apply<boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
               >::apply(geometry1, geometry2, strategy);  
                  ^  
../libs/geometry/extensions/test/gis/latlong/distance_mixed.cpp:83:23: note: in instantiation of function template specialization 'boost::geometry::distance<boost::geometry::model::linestring<boost::geometry::model::ll::point<boost::geometry::degree, double, cs::geographic, 2>, std::vector, std::allocator>, boost::geometry::model::ll::point<boost::geometry::radian, double, cs::geographic, 2>, boost::geometry::strategy::distance::vincenty<double, void> >' requested here  
    BOOST_CHECK_CLOSE(distance(ab, paris_rad, the_strategy), expected, tolerance);  
                      ^  
15 errors generated.  
```  
  
</details>  
  
<details><summary>offset - missing header `boost/geometry/strategies/agnostic/buffer_end_skip.hpp`</summary>  
  
```  
  
  "clang++-9" -c -x c++ -fvisibility-inlines-hidden -Wno-c99-extensions -std=c++2a -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_NO_AUTO_PTR -DBOOST_TIMER_DYN_LINK=1 -I".." -I"../boost/geometry/extensions/contrib/ttmath" -I"../libs/boost/geometry/extensions/contrib/ttmath" -I"../libs/geometry/test" -o "/var/boost/run/results/boost/bin.v2/libs/geometry/extensions/test/algorithms/offset.test/clang-linux-9~c++2a/debug/threading-multi/visibility-hidden/offset.o" "../libs/geometry/extensions/test/algorithms/offset.cpp"  
  
In file included from ../libs/geometry/extensions/test/algorithms/offset.cpp:21:  
../boost/geometry/extensions/algorithms/offset.hpp:20:10: fatal error: 'boost/geometry/strategies/agnostic/buffer_end_skip.hpp' file not found  
#include <boost/geometry/strategies/agnostic/buffer_end_skip.hpp>  
         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
```  
  
</details>  
  
<details><summary>selected - no member named 'make' in namespace 'boost::geometry'</summary>  
  
```  
  "clang++-9" -c -x c++ -fvisibility-inlines-hidden -Wno-c99-extensions -std=c++2a -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_NO_AUTO_PTR -DBOOST_TIMER_DYN_LINK=1 -I".." -I"../boost/geometry/extensions/contrib/ttmath" -I"../libs/boost/geometry/extensions/contrib/ttmath" -I"../libs/geometry/test" -o "/var/boost/run/results/boost/bin.v2/libs/geometry/extensions/test/algorithms/selected.test/clang-linux-9~c++2a/debug/threading-multi/visibility-hidden/selected.o" "../libs/geometry/extensions/test/algorithms/selected.cpp"  
  
../libs/geometry/extensions/test/algorithms/selected.cpp:75:23: error: no member named 'make' in namespace 'boost::geometry'  
    test_selected(bg::make<B>(0,0,4,4), P(2,2), true, 0.001);  
                  ~~~~^  
../libs/geometry/extensions/test/algorithms/selected.cpp:75:28: error: unexpected type name 'B': expected expression  
    test_selected(bg::make<B>(0,0,4,4), P(2,2), true, 0.001);  
                           ^  
../libs/geometry/extensions/test/algorithms/selected.cpp:75:31: warning: expression result unused [-Wunused-value]  
    test_selected(bg::make<B>(0,0,4,4), P(2,2), true, 0.001);  
                              ^  
../libs/geometry/extensions/test/algorithms/selected.cpp:75:35: warning: expression result unused [-Wunused-value]  
    test_selected(bg::make<B>(0,0,4,4), P(2,2), true, 0.001);  
                                  ^  
../libs/geometry/extensions/test/algorithms/selected.cpp:76:23: error: no member named 'make' in namespace 'boost::geometry'  
    test_selected(bg::make<B>(0,0,4,4), P(5,5), false, 0.001);  
                  ~~~~^  
../libs/geometry/extensions/test/algorithms/selected.cpp:76:28: error: unexpected type name 'B': expected expression  
    test_selected(bg::make<B>(0,0,4,4), P(5,5), false, 0.001);  
                           ^  
../libs/geometry/extensions/test/algorithms/selected.cpp:76:31: warning: expression result unused [-Wunused-value]  
    test_selected(bg::make<B>(0,0,4,4), P(5,5), false, 0.001);  
                              ^  
../libs/geometry/extensions/test/algorithms/selected.cpp:76:35: warning: expression result unused [-Wunused-value]  
    test_selected(bg::make<B>(0,0,4,4), P(5,5), false, 0.001);  
                                  ^  
../libs/geometry/extensions/test/algorithms/selected.cpp:77:23: error: no member named 'make' in namespace 'boost::geometry'  
    test_selected(bg::make<B>(0,0,4,4), P(0,0), false, 0.001);  
                  ~~~~^  
../libs/geometry/extensions/test/algorithms/selected.cpp:77:28: error: unexpected type name 'B': expected expression  
    test_selected(bg::make<B>(0,0,4,4), P(0,0), false, 0.001);  
                           ^  
../libs/geometry/extensions/test/algorithms/selected.cpp:77:31: warning: expression result unused [-Wunused-value]  
    test_selected(bg::make<B>(0,0,4,4), P(0,0), false, 0.001);  
                              ^  
../libs/geometry/extensions/test/algorithms/selected.cpp:77:35: warning: expression result unused [-Wunused-value]  
    test_selected(bg::make<B>(0,0,4,4), P(0,0), false, 0.001);  
                                  ^  
../libs/geometry/extensions/test/algorithms/selected.cpp:78:23: error: no member named 'make' in namespace 'boost::geometry'  
    test_selected(bg::make<B>(0,0,4,4), P(4,4), false, 0.001);  
                  ~~~~^  
../libs/geometry/extensions/test/algorithms/selected.cpp:78:28: error: unexpected type name 'B': expected expression  
    test_selected(bg::make<B>(0,0,4,4), P(4,4), false, 0.001);  
                           ^  
../libs/geometry/extensions/test/algorithms/selected.cpp:78:31: warning: expression result unused [-Wunused-value]  
    test_selected(bg::make<B>(0,0,4,4), P(4,4), false, 0.001);  
                              ^  
../libs/geometry/extensions/test/algorithms/selected.cpp:78:35: warning: expression result unused [-Wunused-value]  
    test_selected(bg::make<B>(0,0,4,4), P(4,4), false, 0.001);  
                                  ^  
8 warnings and 8 errors generated.  
```  
  
</details>  
  
Also __boost_check_library__ errors:  
  
```  
libs/geometry/extensions: error: file not found; Did not find [project-root]/index.html file. The file is required for all libraries. Redirection to HTML documentation. <<org-doc-redir>>  
libs/geometry/extensions: error: directory not found; Missing [project-root]/doc directory. The [project-root]/doc directory is required for all libraries. Sources to build with and built documentation for the library. If the library needs to build documentation from non-HTML files this location must be buildable with Boost Build. <<org-doc-dir>>  
libs/geometry/extensions: warning: file not found; Did not find [project-root]/include/boost/[library].h* file. A single header for the library is suggested at [project-root]/include/boost/[library].h* if the library does not have a header directory at [project-root]/include/boost/[library]. <<org-inc-one>>  
libs/geometry/extensions: error: file not found; Did not find [project-root]/meta/libraries.json file, nor did [super-project]/meta/libraries.json contain an entry for the sublibrary. The file is required for all libraries. And contains information about the library used to generate website and documentation for the Boost C++ Libraries collection. <<org-meta-libs>>  
```  
  
```  
libs/geometry/index: error: file not found; Did not find [project-root]/index.html file. The file is required for all libraries. Redirection to HTML documentation. <<org-doc-redir>>  
libs/geometry/index: error: directory not found; Missing [project-root]/doc directory. The [project-root]/doc directory is required for all libraries. Sources to build with and built documentation for the library. If the library needs to build documentation from non-HTML files this location must be buildable with Boost Build. <<org-doc-dir>>  
libs/geometry/index: warning: file not found; Did not find [project-root]/include/boost/[library].h* file. A single header for the library is suggested at [project-root]/include/boost/[library].h* if the library does not have a header directory at [project-root]/include/boost/[library]. <<org-inc-one>>  
libs/geometry/index: error: file not found; Did not find [project-root]/meta/libraries.json file, nor did [super-project]/meta/libraries.json contain an entry for the sublibrary. The file is required for all libraries. And contains information about the library used to generate website and documentation for the Boost C++ Libraries collection. <<org-meta-libs>>  
```

---

## Comment 1

> Username: vissarion  
> Created at: 2019-03-21 13:09:57 UTC  
> Updated at: 2019-03-21 13:18:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/575#issuecomment-475221973  

Thanks for noticing this. At least the ones related to distance and area refer to old code that could be removed or fixed.  @barendgehrels what about dissolve, offset ?

---

## Comment 2

> Username: awulkiew  
> Created at: 2019-03-21 13:24:01 UTC  
> Updated at: 2019-03-21 13:24:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/575#issuecomment-475226750  

The tile sounds scary ;)  
  
These are the extensions which are not part of the released library and are rarely used. Some of them should probably be removed anyway.  
  
**boost_check_library** errors are false positives. We're using Boost regression sublib support to divide regression tests into several pages and at some point they introduced this test for checking directories and files. The easiest way to fix them is to add dummy ones (AFAIR some libraries does this).

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-03-21 13:43:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/575#issuecomment-475233656  

> The tile sounds scary ;)  
  
The repository has no CI and these failures happening for a while already. I thought that you may not aware of the problems.  
  
> boost_check_library errors are false positives.  
  
I know that they are annoying, but they are not false positives, as the check sanitizes the project structure as it is expected for automated tools (regression testing, libraries catalog and documentation generation).

---

## Comment 4

> Username: mloskot  
> Created at: 2019-03-21 14:01:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/575#issuecomment-475240113  

> The repository has no CI and these failures happening for a while already.  
  
What do you mean by that? Boost.Geometry uses CircleCI.

---

## Comment 5

> Username: Kojoley  
> Created at: 2019-03-21 14:04:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/575#issuecomment-475241410  

> What do you mean by that? Boost.Geometry uses CircleCI.  
  
I mean for extensions and index sublibs. Or it has, but for some reason CI pass?

---

## Comment 6

> Username: mloskot  
> Created at: 2019-03-21 14:19:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/575#issuecomment-475247642  

> >  What do you mean by that? Boost.Geometry uses CircleCI.  
>  
> I mean for extensions and index sublibs. Or it has, but for some reason CI pass?  
  
Right. No, you are correct, those extensions are not built by any CI.

---

## Comment 7

> Username: awulkiew  
> Created at: 2019-03-21 19:50:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/575#issuecomment-475377550  

> >boost_check_library errors are false positives.  
  
> I know that they are annoying, but they are not false positives, as the check sanitizes the project structure as it is expected for automated tools (regression testing, libraries catalog and documentation generation).  
  
Do you know of a preferred way of silencing them? I'd like to fix the issue but in the past I figured out that neither putting all of the tests on page (Geometry matrix is already huge) nor adding dummy directories to please the script doesn't seem to be good solutions. So I just ignored it.  
  
> Right. No, you are correct, those extensions are not built by any CI.  
  
The most important (high-level) tests for the index are run by CI, see the previous to last column at e.g.: https://circleci.com/workflow-run/65289579-0d92-4c1d-9d3b-2c3cfbeae204

---

## Comment 8

> Username: Kojoley  
> Created at: 2019-03-21 19:58:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/575#issuecomment-475380188  

> Do you know of a preferred way of silencing them?  
  
I fixed such errors in Spirit https://github.com/boostorg/spirit/commit/49dd0c8fc173ae246413a5a73edce87b7dcab93a (except `doc` one, as Spirit sublibs have documentation).

---

## Comment 9

> Username: mloskot  
> Created at: 2019-03-21 20:22:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/575#issuecomment-475387718  

> Right. No, you are correct, those extensions are not built by any CI.  
>  
> The most important (high-level) tests for the index are run by CI  
  
Yes, for index they are indeed. It's quite clear what is tested:  
  
https://github.com/boostorg/geometry/blob/0426873475c888c4bc69d10551aade139b657a72/.circleci/config.yml#L501-L631

---

## Comment 10

> Username: Kojoley  
> Created at: 2019-03-21 20:43:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/575#issuecomment-475394877  

FYI the index tests `rtree_interprocess_quadratic_dyn`, `rtree_interprocess_rstar_dyn`, and `rtree_interprocess_linear_dyn` are failing on every Clang in C++03 mode.

---

## Comment 11

> Username: awulkiew  
> Created at: 2019-03-21 20:53:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/575#issuecomment-475398245  

> I fixed such errors in Spirit boostorg/spirit@49dd0c8 (except doc one, as Spirit sublibs have documentation).  
  
Right, so in our case this'd mean creation of dummy directories and files to trick the script. Maybe this is the way to go. Especially that one could argue that we're already exploiting the sublibs framework only to divide regression tests.  
  
> FYI the index tests rtree_interprocess_quadratic_dyn, rtree_interprocess_rstar_dyn, and rtree_interprocess_linear_dyn are failing on every Clang in C++03 mode.  
  
Thanks, I'll look into it.

---

## Comment 12

> Username: awulkiew  
> Created at: 2019-03-27 17:47:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/575#issuecomment-477276831  

> FYI the index tests rtree_interprocess_quadratic_dyn, rtree_interprocess_rstar_dyn, and rtree_interprocess_linear_dyn are failing on every Clang in C++03 mode.  
  
related issue https://github.com/boostorg/geometry/issues/577  
fixed in Boost.Container

---

## Comment 13

> Username: awulkiew  
> Created at: 2019-04-08 01:49:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/575#issuecomment-480656097  

Fixed.

---

## Comment 14

> Username: awulkiew  
> Created at: 2019-04-12 13:10:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/575#issuecomment-482568594  

Fixing this issue caused more severe one related to the automatic generation of documentation:  
https://github.com/boostorg/geometry/issues/585  
So it's likely that I'll revert it to the state it was before. Or do you have another idea?
