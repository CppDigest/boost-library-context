# #470 - Geometries with short segments triggers assertions in algorithms [Closed]

> Username: vosst  
> Created at: 2018-04-09 06:11:06 UTC  
> Updated at: 2018-04-12 15:49:42 UTC  
> Closed at: 2018-04-12 15:49:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/470  

The following example:  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
  
namespace bg = boost::geometry;  
  
using DegreePoint = bg::model::d2::point_xy<  
  double,  
  bg::cs::spherical_equatorial<  
    bg::degree  
  >  
>;  
using RadianPoint = bg::model::d2::point_xy<  
  double,  
  bg::cs::spherical_equatorial<  
    bg::radian  
  >  
>;  
using LineString = bg::model::linestring<RadianPoint>;  
  
int main() {  
  std::vector<DegreePoint> ls = {  
    bg::make<DegreePoint>(0.001, 0.001),  
    bg::make<DegreePoint>(0.001, 0.005),  
    bg::make<DegreePoint>(0.005, 0.005)  
  };  
  
  LineString p1;  
  
  bg::append(p1, bg::make<RadianPoint>(bg::get_as_radian<0>(ls[0]), bg::get_as_radian<1>(ls[0])));  
  bg::append(p1, bg::make<RadianPoint>(bg::get_as_radian<0>(ls[1]), bg::get_as_radian<1>(ls[1])));  
  bg::append(p1, bg::make<RadianPoint>(bg::get_as_radian<0>(ls[2]), bg::get_as_radian<1>(ls[2])));  
    
  auto p2 = p1;  
  
  std::cout << std::boolalpha << "equals:     " << boost::geometry::equals(p1, p2) << std::endl;  
  std::cout << std::boolalpha << "intersects: " << boost::geometry::intersects(p1, p2) << std::endl;  
  
  boost::geometry::model::multi_linestring<LineString> out;  
  boost::geometry::intersection(p1, p2, out);  
  
  std::cout << boost::geometry::wkt(out) << std::endl;  
}  
  
```  
triggers an assertion for all three algorithms with the following backtrace:  
```  
gdb ./a.out  
GNU gdb (Ubuntu 8.0.1-0ubuntu1) 8.0.1  
Copyright (C) 2017 Free Software Foundation, Inc.  
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>  
This is free software: you are free to change and redistribute it.  
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"  
and "show warranty" for details.  
This GDB was configured as "x86_64-linux-gnu".  
Type "show configuration" for configuration details.  
For bug reporting instructions, please see:  
<http://www.gnu.org/software/gdb/bugs/>.  
Find the GDB manual and other documentation resources online at:  
<http://www.gnu.org/software/gdb/documentation/>.  
For help, type "help".  
Type "apropos word" to search for commands related to "word"...  
Reading symbols from ./a.out...done.  
(gdb) r  
Starting program: /home/tvoss/Documents/airmap/fix-boost/a.out   
a.out: /usr/include/boost/geometry/policies/robustness/segment_ratio.hpp:54: static bool boost::geometry::detail::segment_ratio::less<Type, false>::apply(const Ratio&, const Ratio&) [with Ratio = boost::geometry::segment_ratio<double>; Type = double]: Assertion `lhs.denominator() != 0' failed.  
equals:       
Program received signal SIGABRT, Aborted.  
__GI_raise (sig=sig@entry=6) at ../sysdeps/unix/sysv/linux/raise.c:51  
51	../sysdeps/unix/sysv/linux/raise.c: No such file or directory.  
(gdb) bt full  
#0  __GI_raise (sig=sig@entry=6) at ../sysdeps/unix/sysv/linux/raise.c:51  
        set = {__val = {0, 140737488342736, 140737488345648, 140737338941713, 139642199179264, 140737338952615, 140737342461728, 3432, 12,   
            93824994873232, 140737342441632, 140737338959746, 93824994875548, 140737342461728, 0, 3432}}  
        pid = <optimized out>  
        tid = <optimized out>  
#1  0x00007ffff713af5d in __GI_abort () at abort.c:90  
        save_stage = 2  
        act = {__sigaction_handler = {sa_handler = 0x5555557d9770, sa_sigaction = 0x5555557d9770}, sa_mask = {__val = {  
              17912167528584999168, 0, 140737354096640, 93824992524722, 54, 93824992531168, 0, 140737488345648, 140737339003982,   
              140737488342768, 140737340166592, 21474836480, 140737340151984, 0, 17912167528584999168, 93824992524722}},   
          sa_flags = -134258688, sa_restorer = 0x55555559b9b2}  
        sigs = {__val = {32, 0 <repeats 15 times>}}  
#2  0x00007ffff7130f17 in __assert_fail_base (fmt=<optimized out>, assertion=assertion@entry=0x55555559b9b2 "lhs.denominator() != 0",   
    file=file@entry=0x55555559b970 "/usr/include/boost/geometry/policies/robustness/segment_ratio.hpp", line=line@entry=54,   
    function=function@entry=0x55555559d2e0 <bool boost::geometry::detail::segment_ratio::less<double, false>::apply<boost::geometry::segment_ratio<double> >(boost::geometry::segment_ratio<double> const&, boost::geometry::segment_ratio<double> const&)::__PRETTY_FUNCTION__> "static bool boost::geometry::detail::segment_ratio::less<Type, false>::apply(const Ratio&, const Ratio&) [with Ratio = boost::geometry::segment_ratio<double>; Type = double]") at assert.c:92  
        str = 0x5555557d9770 ""  
        total = 4096  
#3  0x00007ffff7130fc2 in __GI___assert_fail (assertion=0x55555559b9b2 "lhs.denominator() != 0",   
    file=0x55555559b970 "/usr/include/boost/geometry/policies/robustness/segment_ratio.hpp", line=54,   
    function=0x55555559d2e0 <bool boost::geometry::detail::segment_ratio::less<double, false>::apply<boost::geometry::segment_ratio<double> >(boost::geometry::segment_ratio<double> const&, boost::geometry::segment_ratio<double> const&)::__PRETTY_FUNCTION__> "static bool boost::geometry::detail::segment_ratio::less<Type, false>::apply(const Ratio&, const Ratio&) [with Ratio = boost::geometry::segment_ratio<double>; Type = double]") at assert.c:101  
No locals.  
#4  0x0000555555586c62 in boost::geometry::detail::segment_ratio::less<double, false>::apply<boost::geometry::segment_ratio<double> > (  
    lhs=..., rhs=...) at /usr/include/boost/geometry/policies/robustness/segment_ratio.hpp:54  
        __PRETTY_FUNCTION__ = "static bool boost::geometry::detail::segment_ratio::less<Type, false>::apply(const Ratio&, const Ratio&) [with Ratio = boost::geometry::segment_ratio<double>; Type = double]"  
#5  0x000055555557f398 in boost::geometry::segment_ratio<double>::operator< (this=0x7fffffffd340, other=...)  
    at /usr/include/boost/geometry/policies/robustness/segment_ratio.hpp:203  
No locals.  
#6  0x000055555557a886 in boost::geometry::detail::overlay::base_turn_handler::non_opposite_to_index<boost::geometry::segment_intersection_points<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> > > (info=...) at /usr/include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp:128  
No locals.  
#7  0x0000555555571a4a in boost::geometry::detail::overlay::collinear<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> > >::apply<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_intersection_points<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::geometry::policies::relate::direction_type, boost::geometry::detail::overlay::side_calculator<boost::geometry::spherical_equatorial_tag, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > (pj=...,   
    pk=..., qj=..., qk=..., ti=..., info=..., dir_info=..., side=...)  
    at /usr/include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp:596  
        __PRETTY_FUNCTION__ = "static void boost::geometry::detail::overlay::collinear<TurnInfo>::apply(const Point1&, const Point1&, const Point1&, const Point2&, const Point2&, const Point2&, TurnInfo&, const IntersectionInfo&, c"...  
        arrival = 32767  
        side_p = -11984  
        side_q = 32767  
        side_p_or_q = 1431806492  
        product = 21845  
#8  0x0000555555575953 in boost::geometry::detail::overlay::get_turn_info_linear_linear<boost::geometry::detail::relate::turns::assign_policy<true> >::apply<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> >, boost::geometry::detail::no_rescale_policy, std::back_insert_iterator<std::vector<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::po---Type <return> to continue, or q <return> to quit---    
int_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> >, std::allocator<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> > > > > > (pi=..., pj=..., pk=..., qi=..., qj=..., qk=..., is_p_first=true, is_p_last=false,   
    is_q_first=true, is_q_last=false, tp_model=..., robust_policy=..., out=...)  
    at /usr/include/boost/geometry/algorithms/detail/overlay/get_turn_info_ll.hpp:373  
        method_replace = boost::geometry::detail::overlay::method_touch_interior  
        spike_op = boost::geometry::detail::overlay::operation_continue  
        transformer = {m_method = 4294955592}  
        inters = {<boost::geometry::detail::overlay::intersection_info_base<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::detail::no_rescale_policy>> = {m_side_calc = {m_pi = @0x5555557d8f00, m_pj = @0x5555557d8f10, m_pk = @0x5555557d8f20,   
              m_qi = @0x5555557d8f50, m_qj = @0x5555557d8f60, m_qk = @0x5555557d8f70}},   
          m_result = {<boost::tuples::cons<boost::geometry::segment_intersection_points<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::tuples::cons<boost::geometry::policies::relate::direction_type, boost::tuples::null_type> >> = {head = {count = 1, intersections = {  
                  {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {  
                      m_values = {1.7453292519943296e-05, 1.7453292519943296e-05}}, <No data fields>},   
                  {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {  
                      m_values = {4.635570543470383e-310, 4.6355706688023869e-310}}, <No data fields>}}, fractions = {{robust_ra = {  
                      m_numerator = 0, m_denominator = 1, m_approximation = 0}, robust_rb = {m_numerator = 0, m_denominator = 0,   
                      m_approximation = 0}, initialized = true}, {robust_ra = {m_numerator = 0, m_denominator = 1, m_approximation = 0},   
                    robust_rb = {m_numerator = 0, m_denominator = 1, m_approximation = 0}, initialized = false}}}, tail = {head = {  
                  how = 99 'c', opposite = false, how_a = 0, how_b = 0, dir_a = 0, dir_b = 0, sides = {sides = {{first = 0, second = 0}, {  
                        first = 0, second = 0}}}, arrival = {-1, 0}}}}, <No data fields>}, m_robust_policy = @0x7fffffffd9c7}  
        method = 99 'c'  
        tp = {point = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {  
              m_values = {6.953355807308528e-310, 4.6355705488025864e-310}}, <No data fields>},   
          method = boost::geometry::detail::overlay::method_none, cluster_id = -1, discarded = false, colocated = false,   
          switch_source = false, operations = {elems = {  
              {<boost::geometry::detail::overlay::turn_operation<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >> = {  
                  operation = boost::geometry::detail::overlay::operation_none, seg_id = {source_index = 0, multi_index = -1,   
                    ring_index = -1, segment_index = 0, piece_index = -1}, fraction = {m_numerator = 0, m_denominator = 1,   
                    m_approximation = 0}, remaining_distance = 0}, position = boost::geometry::detail::overlay::position_middle,   
                is_collinear = true},   
              {<boost::geometry::detail::overlay::turn_operation<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >> = {  
                  operation = boost::geometry::detail::overlay::operation_none, seg_id = {source_index = 1, multi_index = -1,   
                    ring_index = -1, segment_index = 0, piece_index = -1}, fraction = {m_numerator = 0, m_denominator = 1,   
                    m_approximation = 0}, remaining_distance = 0}, position = boost::geometry::detail::overlay::position_middle,   
                is_collinear = true}}}}  
#9  0x000055555556cb67 in boost::geometry::detail::get_turns::get_turns_in_sections<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, false, false, boost::geometry::section<boost::geometry::model::box<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > >, 2ul>, boost::geometry::section<boost::geometry::model::box<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > >, 2ul>, boost::geometry::detail::get_turns::get_turn_info_type<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::detail::relate::turns::assign_policy<true>, boost::geometry::linestring_tag, boost::geometry::linestring_tag, boost::geometry::linear_tag, boost::geometry::linear_tag> >::apply<std::vector<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> >, std::allocator<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> > > >, boost::geometry::detail::no_rescale_policy, boost::geometry::detail::relate::linear_linear<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator> >::interrupt_policy_linear_linear<boost::geometry::detail::relate::static_mask_handler<boost::geometry::de9im::static_mask<(char)84, (char)42, (char)70, (char)42, (char)42, (char)70, (char)70, (char)70, (char)42>, true> > > (source_id1=0, geometry1=..., sec1=..., source_id2=1, geometry2=..., sec2=...,   
    skip_larger=false, robust_policy=..., turns=std::vector of length 1, capacity 1 = {...}, interrupt_policy=...)  
---Type <return> to continue, or q <return> to quit---  
    at /usr/include/boost/geometry/algorithms/detail/overlay/get_turns.hpp:296  
        nd_next2 = {<boost::geometry::detail::iterators::iterator_base<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, __gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > >, boost::iterators::bidirectional_traversal_tag>> = {<boost::iterators::iterator_adaptor<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, __gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > >, boost::iterators::use_default, boost::iterators::bidirectional_traversal_tag, boost::iterators::use_default, boost::iterators::use_default>> = {<boost::iterators::iterator_facade<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::iterators::bidirectional_traversal_tag, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const&, long>> = {<boost::iterators::detail::iterator_facade_base<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::iterators::bidirectional_traversal_tag, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const&, long, true, false>> = {<boost::iterators::detail::iterator_facade_base<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::iterators::bidirectional_traversal_tag, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const&, long, false, false>> = {<No data fields>}, <No data fields>}, <No data fields>}, m_iterator =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {8.7266462599716482e-05, 8.7266462599716482e-05}}, <No data fields>}}, <No data fields>}, m_begin =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519943296e-05, 1.7453292519943296e-05}}, <No data fields>}, m_end =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {0, 5.1432233732073765e-321}}, <No data fields>}, m_skip_first = true}  
        size_before = 0  
        is_1_last = false  
        is_2_first = true  
        ti = {point = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {  
              m_values = {6.953355807308528e-310, 4.6355705488025864e-310}}, <No data fields>},   
          method = boost::geometry::detail::overlay::method_none, cluster_id = -1, discarded = false, colocated = false,   
          switch_source = false, operations = {elems = {  
              {<boost::geometry::detail::overlay::turn_operation<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >> = {  
                  operation = boost::geometry::detail::overlay::operation_none, seg_id = {source_index = 0, multi_index = -1,   
                    ring_index = -1, segment_index = 0, piece_index = -1}, fraction = {m_numerator = 0, m_denominator = 1,   
                    m_approximation = 0}, remaining_distance = 0}, position = boost::geometry::detail::overlay::position_middle,   
                is_collinear = false},   
              {<boost::geometry::detail::overlay::turn_operation<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >> = {  
                  operation = boost::geometry::detail::overlay::operation_none, seg_id = {source_index = 1, multi_index = -1,   
                    ring_index = -1, segment_index = 0, piece_index = -1}, fraction = {m_numerator = 0, m_denominator = 1,   
                    m_approximation = 0}, remaining_distance = 0}, position = boost::geometry::detail::overlay::position_middle,   
                is_collinear = false}}}}  
        is_1_first = true  
        is_2_last = false  
        skip = false  
        index2 = 0  
        prev2 =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519943296e-05, 1.7453292519943296e-05}}, <No data fields>}  
        it2 =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519943296e-05, 8.7266462599716482e-05}}, <No data fields>}  
        next2 = {<boost::geometry::detail::iterators::iterator_base<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, __gnu_cxx::__normal_iterator<boost::---Type <return> to continue, or q <return> to quit---  
geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > >, boost::iterators::bidirectional_traversal_tag>> = {<boost::iterators::iterator_adaptor<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, __gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > >, boost::iterators::use_default, boost::iterators::bidirectional_traversal_tag, boost::iterators::use_default, boost::iterators::use_default>> = {<boost::iterators::iterator_facade<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::iterators::bidirectional_traversal_tag, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const&, long>> = {<boost::iterators::detail::iterator_facade_base<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::iterators::bidirectional_traversal_tag, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const&, long, true, false>> = {<boost::iterators::detail::iterator_facade_base<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::iterators::bidirectional_traversal_tag, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const&, long, false, false>> = {<No data fields>}, <No data fields>}, <No data fields>}, m_iterator =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {8.7266462599716482e-05, 8.7266462599716482e-05}}, <No data fields>}}, <No data fields>}, m_begin =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519943296e-05, 1.7453292519943296e-05}}, <No data fields>}, m_end =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {0, 5.1432233732073765e-321}}, <No data fields>}, m_skip_first = true}  
        nd_next1 = {<boost::geometry::detail::iterators::iterator_base<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, __gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > >, boost::iterators::bidirectional_traversal_tag>> = {<boost::iterators::iterator_adaptor<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, __gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > >, boost::iterators::use_default, boost::iterators::bidirectional_traversal_tag, boost::iterators::use_default, boost::iterators::use_default>> = {<boost::iterators::iterator_facade<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::iterators::bidirectional_traversal_tag, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const&, long>> = {<boost::iterators::detail::iterator_facade_base<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::iterators::bidirectional_traversal_tag, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const&, long, true, false>> = {<boost::iterators::detail::iterator_facade_base<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::iterators::bidirectional_traversal_tag, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const&, long, false, false>> = {<No data fields>}, <No data fields>}, <No data fields>}, m_iterator =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {8.7266462599716482e-05, 8.7266462599716482e-05}}, <No data fields>}}, <No data fields>}, m_begin =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519943296e-05, 1.7453292519943296e-05}}, <No data fields>}, m_end =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {0, 0}}---Type <return> to continue, or q <return> to quit---  
, <No data fields>}, m_skip_first = true}  
        ndi2 = 0  
        end2 =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {8.7266462599716482e-05, 8.7266462599716482e-05}}, <No data fields>}  
        cview1 = {m_range = @0x7fffffffdbf0}  
        cview2 = {m_range = @0x7fffffffdc10}  
        view1 = {m_range = @0x7fffffffd4e8}  
        view2 = {m_range = @0x7fffffffd4f0}  
        begin_range_1 =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519943296e-05, 1.7453292519943296e-05}}, <No data fields>}  
        end_range_1 =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {0, 0}}, <No data fields>}  
        begin_range_2 =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519943296e-05, 1.7453292519943296e-05}}, <No data fields>}  
        end_range_2 =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {0, 5.1432233732073765e-321}}, <No data fields>}  
        dir1 = 0  
        dir2 = 0  
        index1 = 0  
        ndi1 = 0  
        same_source = false  
        prev1 =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519943296e-05, 1.7453292519943296e-05}}, <No data fields>}  
        it1 =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519943296e-05, 8.7266462599716482e-05}}, <No data fields>}  
        end1 =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {8.7266462599716482e-05, 8.7266462599716482e-05}}, <No data fields>}  
        next1 = {<boost::geometry::detail::iterators::iterator_base<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, __gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > >, boost::iterators::bidirectional_traversal_tag>> = {<boost::iterators::iterator_adaptor<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, __gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > >, boost::iterators::use_default, boost::iterators::bidirectional_traversal_tag, boost::iterators::use_default, boost::iterators::use_default>> = {<boost::iterators::iterator_facade<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::iterators::bidirectional_traversal_tag, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const&, long>> = {<boost::iterators::detail::iterator_facade_base<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::iterators::bidirectional_traversal_tag, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const&, long, true, false>> = {<boost::iterators::detail::iterator_facade_base<boost::geometry::ever_circling_iterator<__gnu_cxx::__normal_iterator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const*, std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > > > >, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::iterators::bidirectional_traversal_tag, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > const&, long, false, false>> = {<No data fields>}, <No data fields>}, <No data fields>}, m_iterator =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {8.7266462599716482e-05, 8.7266462599716482e-05}}, <No data fields>}}, <No data fields>}, m_begin =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519943296e-05, 1.7453292519943296e-05}}, <No data fields>}, m_end =   
      {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {0, 0}}---Type <return> to continue, or q <return> to quit---  
, <No data fields>}, m_skip_first = true}  
#10 0x00005555555662e3 in boost::geometry::detail::get_turns::section_visitor<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, false, false, std::vector<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> >, std::allocator<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> > > >, boost::geometry::detail::get_turns::get_turn_info_type<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::detail::relate::turns::assign_policy<true>, boost::geometry::linestring_tag, boost::geometry::linestring_tag, boost::geometry::linear_tag, boost::geometry::linear_tag>, boost::geometry::detail::no_rescale_policy, boost::geometry::detail::relate::linear_linear<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator> >::interrupt_policy_linear_linear<boost::geometry::detail::relate::static_mask_handler<boost::geometry::de9im::static_mask<(char)84, (char)42, (char)70, (char)42, (char)42, (char)70, (char)70, (char)70, (char)42>, true> > >::apply<boost::geometry::section<boost::geometry::model::box<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > >, 2ul> > (this=0x7fffffffd8f0, sec1=..., sec2=...) at /usr/include/boost/geometry/algorithms/detail/overlay/get_turns.hpp:427  
No locals.  
#11 0x0000555555561e17 in boost::geometry::partition<boost::geometry::model::box<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > >, boost::geometry::detail::section::get_section_box, boost::geometry::detail::section::overlaps_section_box, boost::geometry::detail::section::get_section_box, boost::geometry::detail::section::overlaps_section_box, boost::geometry::detail::partition::include_all_policy, boost::geometry::detail::partition::include_all_policy, boost::geometry::detail::partition::visit_no_policy>::apply<boost::geometry::sections<boost::geometry::model::box<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > >, 2ul>, boost::geometry::sections<boost::geometry::model::box<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > >, 2ul>, boost::geometry::detail::get_turns::section_visitor<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, false, false, std::vector<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> >, std::allocator<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> > > >, boost::geometry::detail::get_turns::get_turn_info_type<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::detail::relate::turns::assign_policy<true>, boost::geometry::linestring_tag, boost::geometry::linestring_tag, boost::geometry::linear_tag, boost::geometry::linear_tag>, boost::geometry::detail::no_rescale_policy, boost::geometry::detail::relate::linear_linear<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator> >::interrupt_policy_linear_linear<boost::geometry::detail::relate::static_mask_handler<boost::geometry::de9im::static_mask<(char)84, (char)42, (char)70, (char)42, (char)42, (char)70, (char)70, (char)70, (char)42>, true> > > > (forward_range1=..., forward_range2=..., visitor=..., min_elements=16, box_visitor=...)  
    at /usr/include/boost/geometry/algorithms/detail/partition.hpp:627  
        it2 =   
  {static dimension_count = 2, directions = {0, 1}, ring_id = {source_index = 1, multi_index = -1, ring_index = -1}, bounding_box = {m_min_corner = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519721252e-05, 1.7453292519721252e-05}}, <No data fields>}, m_max_corner = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292520165341e-05, 8.7266462599938527e-05}}, <No data fields>}}, begin_index = 0, end_index = 1, count = 1, range_count = 3, duplicate = false, non_duplicate_index = 0, is_non_duplicate_first = true, is_non_duplicate_last = false}  
        it1 =   
  {static dimension_count = 2, directions = {0, 1}, ring_id = {source_index = 0, multi_index = -1, ring_index = -1}, bounding_box = {m_min_corner = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519721252e-05, 1.7453292519721252e-05}}, <No data fields>}, m_max_corner = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292520165341e-05, 8.7266462599938527e-05}}, <No data fields>}}, begin_index = 0, end_index = 1, count = 1, range_count = 3, duplicate = false, non_duplicate_index = 0, is_non_duplicate_first = true, is_non_duplicate_last = false}  
#12 0x000055555555e4ad in boost::geometry::detail::get_turns::get_turns_generic<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vecto---Type <return> to continue, or q <return> to quit---  
r, std::allocator>, false, false, boost::geometry::detail::get_turns::get_turn_info_type<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::detail::relate::turns::assign_policy<true>, boost::geometry::linestring_tag, boost::geometry::linestring_tag, boost::geometry::linear_tag, boost::geometry::linear_tag> >::apply<boost::geometry::detail::no_rescale_policy, std::vector<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> >, std::allocator<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> > > >, boost::geometry::detail::relate::linear_linear<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator> >::interrupt_policy_linear_linear<boost::geometry::detail::relate::static_mask_handler<boost::geometry::de9im::static_mask<(char)84, (char)42, (char)70, (char)42, (char)42, (char)70, (char)70, (char)70, (char)42>, true> > > (source_id1=0,   
    geometry1=..., source_id2=1, geometry2=..., robust_policy=..., turns=std::vector of length 1, capacity 1 = {...}, interrupt_policy=...)  
    at /usr/include/boost/geometry/algorithms/detail/overlay/get_turns.hpp:486  
        sec1 = {<std::vector<boost::geometry::section<boost::geometry::model::box<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > >, 2>, std::allocator<boost::geometry::section<boost::geometry::model::box<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > >, 2> > >> = std::vector of length 2, capacity 2 = {{static dimension_count = 2, directions = {0, 1}, ring_id = {source_index = 0, multi_index = -1, ring_index = -1},   
              bounding_box = {  
                m_min_corner = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519721252e-05, 1.7453292519721252e-05}}, <No data fields>},   
                m_max_corner = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292520165341e-05, 8.7266462599938527e-05}}, <No data fields>}}, begin_index = 0, end_index = 1, count = 1,   
              range_count = 3, duplicate = false, non_duplicate_index = 0, is_non_duplicate_first = true, is_non_duplicate_last = false}, {  
              static dimension_count = 2, directions = {1, 0}, ring_id = {source_index = 0, multi_index = -1, ring_index = -1},   
              bounding_box = {  
                m_min_corner = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519721252e-05, 8.7266462599494438e-05}}, <No data fields>},   
                m_max_corner = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {8.7266462599938527e-05, 8.7266462599938527e-05}}, <No data fields>}}, begin_index = 1, end_index = 2, count = 1,   
              range_count = 3, duplicate = false, non_duplicate_index = 1, is_non_duplicate_first = false, is_non_duplicate_last = true}},   
  
  
          static value = <optimized out>}  
        sec2 = {<std::vector<boost::geometry::section<boost::geometry::model::box<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > >, 2>, std::allocator<boost::geometry::section<boost::geometry::model::box<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > >, 2> > >> = std::vector of length 2, capacity 2 = {{static dimension_count = 2, directions = {0, 1}, ring_id = {source_index = 1, multi_index = -1, ring_index = -1},   
              bounding_box = {  
                m_min_corner = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519721252e-05, 1.7453292519721252e-05}}, <No data fields>},   
                m_max_corner = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292520165341e-05, 8.7266462599938527e-05}}, <No data fields>}}, begin_index = 0, end_index = 1, count = 1,   
              range_count = 3, duplicate = false, non_duplicate_index = 0, is_non_duplicate_first = true, is_non_duplicate_last = false}, {  
              static dimension_count = 2, directions = {1, 0}, ring_id = {source_index = 1, multi_index = -1, ring_index = -1},   
              bounding_box = {  
                m_min_corner = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {1.7453292519721252e-05, 8.7266462599494438e-05}}, <No data fields>},   
                m_max_corner = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {m_values = {8.7266462599938527e-05, 8.7266462599938527e-05}}, <No data fields>}}, begin_index = 1, end_index = 2, count = 1,   
              range_count = 3, duplicate = false, non_duplicate_index = 1, is_non_duplicate_first = false, is_non_duplicate_last = true}},   
          static value = <optimized out>}  
        visitor = {m_source_id1 = 0, m_geometry1 = @0x7fffffffdbf0, m_source_id2 = 1, m_geometry2 = @0x7fffffffdc10,   
          m_rescale_policy = @0x7fffffffd9c7, m_turns = std::vector of length 1, capacity 1 = {{  
              point = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {  
                  m_values = {1.7453292519943296e-05, 1.7453292519943296e-05}}, <No data fields>},   
              method = boost::geometry::detail::overlay::method_touch, cluster_id = -1, discarded = false, colocated = false,   
              switch_source = false, operations = {elems = {  
                  {<boost::geometry::detail::overlay::turn_operation<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >> = {  
                      operation = boost::geometry::detail::overlay::operation_intersection, seg_id = {source_index = 0, multi_index = -1,   
                        ring_index = -1, segment_index = 0, piece_index = -1}, fraction = {m_numerator = 0, m_denominator = 1,   
                        m_approximation = 0}, remaining_distance = 0}, position = boost::geometry::detail::overlay::position_front,   
                    is_collinear = false},   
                  {<boost::geometry::detail::overlay::turn_operation<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >> = {  
                      operation = boost::geometry::detail::overlay::operation_intersection, seg_id = {source_index = 1, multi_index = -1,   
                        ring_index = -1, segment_index = 0, piece_index = -1}, fraction = {m_numerator = 0, m_denominator = 0,   
---Type <return> to continue, or q <return> to quit---  
                        m_approximation = 0}, remaining_distance = 0}, position = boost::geometry::detail::overlay::position_middle,   
                    is_collinear = false}}}}}, m_interrupt_policy = @0x7fffffffda08}  
#13 0x000055555555b5aa in boost::geometry::detail::relate::turns::get_turns<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::detail::get_turns::get_turn_info_type<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::detail::relate::turns::assign_policy<true>, boost::geometry::linestring_tag, boost::geometry::linestring_tag, boost::geometry::linear_tag, boost::geometry::linear_tag>, boost::geometry::detail::no_rescale_policy>::apply<std::vector<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> >, std::allocator<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> > > >, boost::geometry::detail::relate::linear_linear<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator> >::interrupt_policy_linear_linear<boost::geometry::detail::relate::static_mask_handler<boost::geometry::de9im::static_mask<(char)84, (char)42, (char)70, (char)42, (char)42, (char)70, (char)70, (char)70, (char)42>, true> > > (  
    turns=std::vector of length 1, capacity 1 = {...}, geometry1=..., geometry2=..., interrupt_policy=..., robust_policy=...)  
    at /usr/include/boost/geometry/algorithms/detail/relate/turns.hpp:121  
No locals.  
#14 0x0000555555559e5b in boost::geometry::detail::relate::turns::get_turns<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::detail::get_turns::get_turn_info_type<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::detail::relate::turns::assign_policy<true>, boost::geometry::linestring_tag, boost::geometry::linestring_tag, boost::geometry::linear_tag, boost::geometry::linear_tag>, boost::geometry::detail::no_rescale_policy>::apply<std::vector<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> >, std::allocator<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double>, boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, boost::array<boost::geometry::detail::overlay::turn_operation_linear<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >, 2ul> > > >, boost::geometry::detail::relate::linear_linear<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator> >::interrupt_policy_linear_linear<boost::geometry::detail::relate::static_mask_handler<boost::geometry::de9im::static_mask<(char)84, (char)42, (char)70, (char)42, (char)42, (char)70, (char)70, (char)70, (char)42>, true> > > (  
    turns=std::vector of length 1, capacity 1 = {...}, geometry1=..., geometry2=..., interrupt_policy=...)  
    at /usr/include/boost/geometry/algorithms/detail/relate/turns.hpp:92  
        robust_policy = {static enabled = false}  
#15 0x000055555555906f in boost::geometry::detail::relate::linear_linear<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator> >::apply<boost::geometry::detail::relate::static_mask_handler<boost::geometry::de9im::static_mask<(char)84, (char)42, (char)70, (char)42, (char)42, (char)70, (char)70, (char)70, (char)42>, true> > (geometry1=..., geometry2=..., result=...)  
    at /usr/include/boost/geometry/algorithms/detail/relate/linear_linear.hpp:141  
        turns = std::vector of length 1, capacity 1 = {{  
            point = {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {  
                m_values = {1.7453292519943296e-05, 1.7453292519943296e-05}}, <No data fields>},   
            method = boost::geometry::detail::overlay::method_touch, cluster_id = -1, discarded = false, colocated = false,   
            switch_source = false, operations = {elems = {  
                {<boost::geometry::detail::overlay::turn_operation<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >> = {  
                    operation = boost::geometry::detail::overlay::operation_intersection, seg_id = {source_index = 0, multi_index = -1,   
                      ring_index = -1, segment_index = 0, piece_index = -1}, fraction = {m_numerator = 0, m_denominator = 1,   
                      m_approximation = 0}, remaining_distance = 0}, position = boost::geometry::detail::overlay::position_front,   
                  is_collinear = false},   
                {<boost::geometry::detail::overlay::turn_operation<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, boost::geometry::segment_ratio<double> >> = {  
                    operation = boost::geometry::detail::overlay::operation_intersection, seg_id = {source_index = 1, multi_index = -1,   
                      ring_index = -1, segment_index = 0, piece_index = -1}, fraction = {m_numerator = 0, m_denominator = 0,   
---Type <return> to continue, or q <return> to quit---  
                      m_approximation = 0}, remaining_distance = 0}, position = boost::geometry::detail::overlay::position_middle,   
                  is_collinear = false}}}}}  
        interrupt_policy = {static enabled = true, m_result = @0x7fffffffdb3e}  
        boundary_checker1 = {has_boundary = 16, geometry = @0x7ffff74dcc10}  
        pred1 = {m_result = @0x0, m_boundary_checker = @0x0, m_flags = 4149053600}  
        boundary_checker2 = {has_boundary = 13, geometry = @0xd}  
        pred2 = {m_result = @0xff00000000000000, m_boundary_checker = @0x0, m_flags = 125}  
#16 0x0000555555558676 in boost::geometry::detail::relate::relate_impl<boost::geometry::detail::de9im::static_mask_equals_type, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator> >::apply (g1=..., g2=...)  
    at /usr/include/boost/geometry/algorithms/detail/relate/relate_impl.hpp:69  
        handler = {<boost::geometry::detail::relate::matrix_handler<boost::geometry::detail::relate::matrix<3, 3> >> = {  
            static interrupt = <optimized out>, m_matrix = {static static_width = 3, static static_height = 3, static static_size = 9,   
              m_array = "FFFFFFFFF"}}, interrupt = false}  
#17 0x0000555555557e63 in boost::geometry::resolve_variant::equals<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator> >::apply (geometry1=..., geometry2=...) at /usr/include/boost/geometry/algorithms/equals.hpp:362  
No locals.  
#18 0x0000555555557661 in boost::geometry::equals<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator> > (  
    geometry1=..., geometry2=...) at /usr/include/boost/geometry/algorithms/equals.hpp:478  
No locals.  
#19 0x0000555555555c04 in main () at test.cpp:37  
        wkt = 0x55555559b478 "LINESTRING(0.001 0.001,0.001 0.005,0.005 0.005)"  
        ls = std::vector of length 3, capacity 3 = {  
          {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::degree> >> = {m_values = {  
                0.001, 0.001}}, <No data fields>},   
          {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::degree> >> = {m_values = {  
                0.001, 0.0050000000000000001}}, <No data fields>},   
          {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::degree> >> = {m_values = {  
                0.0050000000000000001, 0.0050000000000000001}}, <No data fields>}}  
        p1 = {<std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > >> = std::vector of length 3, capacity 4 = {  
            {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {  
                m_values = {1.7453292519943296e-05, 1.7453292519943296e-05}}, <No data fields>},   
            {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {  
                m_values = {1.7453292519943296e-05, 8.7266462599716482e-05}}, <No data fields>},   
            {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {  
                m_values = {8.7266462599716482e-05, 8.7266462599716482e-05}}, <No data fields>}}, <No data fields>}  
        p2 = {<std::vector<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::allocator<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> > > >> = std::vector of length 3, capacity 3 = {  
            {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {  
                m_values = {1.7453292519943296e-05, 1.7453292519943296e-05}}, <No data fields>},   
            {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {  
                m_values = {1.7453292519943296e-05, 8.7266462599716482e-05}}, <No data fields>},   
            {<boost::geometry::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >> = {  
                m_values = {8.7266462599716482e-05, 8.7266462599716482e-05}}, <No data fields>}}, <No data fields>}  
        out = {<std::vector<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator>, std::allocator<boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::spherical_equatorial<boost::geometry::radian> >, std::vector, std::allocator> > >> = std::vector of length -189413617304512226, capacity -189415571991743554 = {<error reading variable>  
```

---

## Comment 1

> Username: vosst  
> Created at: 2018-04-09 06:11:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/470#issuecomment-379644203  

@remster

---

## Comment 2

> Username: vosst  
> Created at: 2018-04-09 10:26:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/470#issuecomment-379706502  

Some additional data points when using `boost::multiprecision`:  
```  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
  
#include <iostream>  
  
namespace bg = boost::geometry;  
namespace bm = boost::multiprecision;  
  
using DegreePoint = bg::model::d2::point_xy<  
  bm::number<bm::cpp_dec_float<100>, bm::et_off>,  
  bg::cs::spherical_equatorial<  
    bg::degree  
  >  
>;  
  
using LineString = bg::model::linestring<DegreePoint>;  
  
int main() {  
  std::vector<DegreePoint> ls = {  
    bg::make<DegreePoint>(0.001, 0.001),  
    bg::make<DegreePoint>(0.001, 0.005),  
    bg::make<DegreePoint>(0.005, 0.005)  
  };  
  
  LineString p1;  
  
  bg::append(p1, bg::make<DegreePoint>(0.001, 0.001));  
  bg::append(p1, bg::make<DegreePoint>(0.001, 0.005));  
  bg::append(p1, bg::make<DegreePoint>(0.005, 0.005));  
    
  auto p2 = p1;  
  
  std::cout << std::boolalpha << "equals:     " << boost::geometry::equals(p1, p2) << std::endl;  
  std::cout << std::boolalpha << "intersects: " << boost::geometry::intersects(p1, p2) << std::endl;  
  
  boost::geometry::model::multi_linestring<LineString> out;  
  boost::geometry::intersection(p1, p2, out);  
  
  std::cout << boost::geometry::wkt(out) << std::endl;  
}  
```  
Produces the following output:  
```  
> ./test  
equals:     false  
intersects: false  
MULTILINESTRING()  
```

---

## Comment 3

> Username: vosst  
> Created at: 2018-04-09 10:27:48 UTC  
> Updated at: 2018-04-09 10:27:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/470#issuecomment-379706944  

Please note that I had to patch `boost::geometry` 1.66.0 to avoid using `std::acos` and relying on `fmod` instead of `mod` for non-fundamental arithmetic types (in `boost/geometry/util/math.hpp`).

---

## Comment 4

> Username: awulkiew  
> Created at: 2018-04-09 12:58:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/470#issuecomment-379742684  

Could you check if the fix below works for you also in other cases?  
https://github.com/awulkiew/geometry/commit/032d208ce4025b3c28b02f7ad889c4017af6d78a#diff-d4ee7ff3305557a348687bbe7cd5cb6c

---

## Comment 5

> Username: vosst  
> Created at: 2018-04-09 13:18:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/470#issuecomment-379748432  

@awulkiew I can confirm that your patch fixes the issue for my isolated test-case. @remster could you give it a spin on a more relevant workload?

---

## Comment 6

> Username: vosst  
> Created at: 2018-04-09 13:21:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/470#issuecomment-379749380  

@awulkiew under the assumption that the patch stands up to real-world work-loads, any chance that you would consider backporting to the 1.66.x series?.

---

## Comment 7

> Username: awulkiew  
> Created at: 2018-04-09 13:38:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/470#issuecomment-379754881  

@vosst At Boost we typically just release the next version. Very rarely if something goes seriously wrong near the end of planned release (but we cannot stop the release at that point) we release an updated minor version. So at this point it is not possible to officially release 1.66.1. I could prepare a branch based on 1.66.0 with this patch but this is also something you could do by yourself. Or do you have something different in mind?

---

## Comment 8

> Username: vosst  
> Created at: 2018-04-09 13:50:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/470#issuecomment-379758871  

@awulkiew nope, that answers my question, thanks. We will just maintain a patch on our source tree that we apply to the boost 1.66 sources.

---

## Comment 9

> Username: awulkiew  
> Created at: 2018-04-09 20:47:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/470#issuecomment-379888900  

@vosst @remster Please let me know after running the tests if this fix works for you.

---

## Comment 10

> Username: remster  
> Created at: 2018-04-11 17:04:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/470#issuecomment-380525576  

@awulkiew : sorry for the slight delay. Your patch appears to work on a much larger (~10000) set of our real-life geometries. Perhaps too early for 100% certainty that all is correct, but certainly enough to get excited. Thank you.

---

## Comment 11

> Username: awulkiew  
> Created at: 2018-04-12 14:25:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/470#issuecomment-380824001  

@remster Ok, thanks for the info.
