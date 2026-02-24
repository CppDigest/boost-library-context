# #784 - Build failed in some examples [Closed]

> Username: Siddharth-coder13  
> Created at: 2020-12-17 10:14:31 UTC  
> Updated at: 2020-12-23 09:30:05 UTC  
> Closed at: 2020-12-23 09:30:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/784  

As I was trying to build the examples,  I found `4` programs in the `examples` directory, which failed during compilations. After spending some time, it seems to me that these `programs` may have not been updated with the other `repos` .   
As for example, on compiling `05_b_overlay_linestring_polygon_example.cpp`   
  
    05_b_overlay_linestring_polygon_example.cpp: In function ‘int main()’:  
    05_b_overlay_linestring_polygon_example.cpp:70:17: error: ‘segment_ratio_type’ in namespace ‘bg’ does not name a template   
    type; did you mean ‘segment_ratio’?  
        70 |     typedef bg::segment_ratio_type<point_2d, bg::detail::no_rescale_policy>::type segment_ratio;  
          |                 ^~~~~~~~~~~~~~~~~~  
          |                 segment_ratio  
    05_b_overlay_linestring_polygon_example.cpp:71:54: error: ‘segment_ratio’ was not declared in this scope  
       71 |     typedef bg::detail::overlay::turn_info<point_2d, segment_ratio> turn_info;  
          |                                                      ^~~~~~~~~~~~~  
    05_b_overlay_linestring_polygon_example.cpp:71:54: note: suggested alternatives:  
    In file included from ../../../boost/geometry/policies/robustness/no_rescale_policy.hpp:19,  
                     from ../../../boost/geometry/policies/robustness/rescale_policy_tags.hpp:12,  
                     from ../../../boost/geometry/policies/robustness/segment_ratio_type.hpp:24,  
                     from ../../../boost/geometry/strategies/intersection_strategies.hpp:20,  
                     from ../../../boost/geometry/strategies/strategies.hpp:37,  
                     from ../../../boost/geometry/geometry.hpp:58,  
                     from 05_b_overlay_linestring_polygon_example.cpp:25:  
    ../../../boost/geometry/policies/robustness/segment_ratio.hpp:107:7: note:   ‘boost::geometry::segment_ratio’  
      107 | class segment_ratio  
          |       ^~~~~~~~~~~~~  
    ../../../boost/geometry/policies/robustness/segment_ratio.hpp:29:30: note:   ‘boost::geometry::detail::segment_ratio’  
       29 | namespace detail { namespace segment_ratio  
          |                              ^~~~~~~~~~~~~  
    05_b_overlay_linestring_polygon_example.cpp:71:67: error: template argument 2 is invalid  
       71 |     typedef bg::detail::overlay::turn_info<point_2d, segment_ratio> turn_info;  
          |                                                                   ^  
    05_b_overlay_linestring_polygon_example.cpp:71:67: error: template argument 3 is invalid  
    05_b_overlay_linestring_polygon_example.cpp:71:67: error: template argument 4 is invalid  
    05_b_overlay_linestring_polygon_example.cpp:75:110: error: no matching function for call to ‘get_turns<false, false,   
    boost::geometry::detail::overlay::assign_null_policy>   
    (boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double> >&,   
    boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double> >&,   
    boost::geometry::detail::no_rescale_policy&, std::vector<int>&, boost::geometry::detail::get_turns::no_interrupt_policy&)’  
       75 |     bg::get_turns<false, false, bg::detail::overlay::assign_null_policy>(ls, p, rescale_policy, turns, policy);  
          |                                                                                                              ^  
    In file included from ../../../boost/geometry/algorithms/detail/relate/turns.hpp:20,  
                     from ../../../boost/geometry/algorithms/detail/relate/linear_linear.hpp:32,  
                     from ../../../boost/geometry/algorithms/detail/relate/implementation.hpp:24,  
                     from ../../../boost/geometry/algorithms/relate.hpp:15,  
                     from ../../../boost/geometry/algorithms/detail/within/implementation.hpp:43,  
                     from ../../../boost/geometry/algorithms/detail/covered_by/implementation.hpp:25,  
                     from ../../../boost/geometry/algorithms/covered_by.hpp:24,  
                     from ../../../boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp:32,  
                     from ../../../boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp:29,  
                     from ../../../boost/geometry/algorithms/buffer.hpp:41,  
                     from ../../../boost/geometry/geometry.hpp:63,  
                     from 05_b_overlay_linestring_polygon_example.cpp:25:  
    ../../../boost/geometry/algorithms/detail/overlay/get_turns.hpp:1148:13: note: candidate: ‘template<bool Reverse1, bool   
    Reverse2, class AssignPolicy, class Geometry1, class Geometry2, class IntersectionStrategy, class RobustPolicy, class Turns,   
    class InterruptPolicy> void boost::geometry::get_turns(const Geometry1&, const Geometry2&, const IntersectionStrategy&,   
    const RobustPolicy&, Turns&, InterruptPolicy&)’  
     1148 | inline void get_turns(Geometry1 const& geometry1,  
          |             ^~~~~~~~~  
    ../../../boost/geometry/algorithms/detail/overlay/get_turns.hpp:1148:13: note:   template argument deduction/substitution   
    failed:  
    05_b_overlay_linestring_polygon_example.cpp:75:110: note:   candidate expects 6 arguments, 5 provided  
       75 |     bg::get_turns<false, false, bg::detail::overlay::assign_null_policy>(ls, p, rescale_policy, turns, policy);  
          |                                                                                                              ^  
    05_b_overlay_linestring_polygon_example.cpp:81:18: error: request for member ‘operations’ in ‘turn’, which is of non-class   
    type ‘const turn_info’ {aka ‘const int’}  
       81 |         if (turn.operations[0].operation  
          |                  ^~~~~~~~~~  
    05_b_overlay_linestring_polygon_example.cpp:86:23: error: request for member ‘operations’ in ‘turn’, which is of non-class   
    type ‘const turn_info’ {aka ‘const int’}  
       86 |         else if (turn.operations[0].operation  
          |                       ^~~~~~~~~~  
    05_b_overlay_linestring_polygon_example.cpp:92:63: error: request for member ‘point’ in ‘turn’, which is of non-class type ‘   
    const turn_info’ {aka ‘const int’}  
       92 |         std::cout << action << " polygon at " << bg::dsv(turn.point) << std::endl;  
          |                                                               ^~~~~  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1  -   
     I"../../.."  -c -o "../../../bin.v2/libs/geometry/example/gcc-9/debug/threading-multi/visibility-   
     hidden/05_b_overlay_linestring_polygon_example.o" "05_b_overlay_linestring_polygon_example.cpp"  
  
    ...failed gcc.compile.c++ ../../../bin.v2/libs/geometry/example/gcc-9/debug/threading-multi/visibility-   
    hidden/05_b_overlay_linestring_polygon_example.o...  
  
In the worst case, there may be some issues with the header files.

---

## Comment 1

> Username: GauravSarkar  
> Created at: 2020-12-22 16:55:39 UTC  
> Updated at: 2020-12-22 17:25:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/784#issuecomment-749655624  

```  
garry@LAPTOP-ORBHSDR0:~/geometry/example$ g++ 05_b_overlay_linestring_polygon_example.cpp  
05_b_overlay_linestring_polygon_example.cpp: In function ‘int main()’:  
05_b_overlay_linestring_polygon_example.cpp:75:110: error: no matching function for call to ‘get_turns<false, false, boost::geometry::detail::overlay::assign_null_policy>(boost::geometry::model::linestring<boost::geometry::model::d2::point_xy<double> >&, boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double> >&, boost::geometry::detail::no_rescale_policy&, std::vector<boost::geometry::detail::overlay::turn_info<boost::geometry::model::d2::point_xy<double>, boost::geometry::segment_ratio<double> > >&, boost::geometry::detail::get_turns::no_interrupt_policy&)’  
     bg::get_turns<false, false, bg::detail::overlay::assign_null_policy>(ls, p, rescale_policy, turns, policy);  
                                                                                                              ^  
In file included from /usr/include/boost/geometry/algorithms/detail/relate/turns.hpp:20,  
                 from /usr/include/boost/geometry/algorithms/detail/relate/linear_linear.hpp:32,  
                 from /usr/include/boost/geometry/algorithms/detail/relate/implementation.hpp:24,  
                 from /usr/include/boost/geometry/algorithms/relate.hpp:15,  
                 from /usr/include/boost/geometry/algorithms/detail/within/implementation.hpp:46,  
                 from /usr/include/boost/geometry/algorithms/detail/covered_by/implementation.hpp:26,  
                 from /usr/include/boost/geometry/algorithms/covered_by.hpp:24,  
                 from /usr/include/boost/geometry/algorithms/detail/distance/point_to_geometry.hpp:42,  
                 from /usr/include/boost/geometry/strategies/cartesian/centroid_weighted_length.hpp:25,  
                 from /usr/include/boost/geometry/strategies/strategies.hpp:58,  
                 from /usr/include/boost/geometry/geometry.hpp:49,  
                 from 05_b_overlay_linestring_polygon_example.cpp:25:  
/usr/include/boost/geometry/algorithms/detail/overlay/get_turns.hpp:1011:13: note: candidate: ‘template<bool Reverse1, bool Reverse2, class AssignPolicy, class Geometry1, class Geometry2, class IntersectionStrategy, class RobustPolicy, class Turns, class InterruptPolicy> void boost::geometry::get_turns(const Geometry1&, const Geometry2&, const IntersectionStrategy&, const RobustPolicy&, Turns&, InterruptPolicy&)’  
 inline void get_turns(Geometry1 const& geometry1,  
             ^~~~~~~~~  
/usr/include/boost/geometry/algorithms/detail/overlay/get_turns.hpp:1011:13: note:   template argument deduction/substitution failed:  
05_b_overlay_linestring_polygon_example.cpp:75:110: note:   candidate expects 6 arguments, 5 provided  
     bg::get_turns<false, false, bg::detail::overlay::assign_null_policy>(ls, p, rescale_policy, turns, policy);  
```  
  
I am being given this error when I compiled 05_b_overlay_linestring_polygon_example.cpp.I guess some requirements are missing.Further read through docs might help.

---

## Comment 2

> Username: mloskot  
> Created at: 2020-12-22 17:08:34 UTC  
> Updated at: 2020-12-22 17:09:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/784#issuecomment-749663793  

@GauravSarkar Please, learn how to format log/code snippets on GitHub, https://docs.github.com/en/free-pro-team@latest/github/writing-on-github/creating-and-highlighting-code-blocks  
  
  
Please, consider the simplistic `g++ 05_b_overlay_linestring_polygon_example.cpp` that you execute: how compiler should know where to look for all the required headers?  
  
https://github.com/boostorg/geometry/blob/6ce78ef31a6ad1c15f28003ec189836ee7fda832/example/05_b_overlay_linestring_polygon_example.cpp#L22-L33

---

## Comment 3

> Username: mloskot  
> Created at: 2020-12-22 18:30:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/784#issuecomment-749707716  

@Siddharth-coder13 Thanks for the report.  
  
PR #787 should fix the compilation of all the examples.

---

## Comment 4

> Username: GauravSarkar  
> Created at: 2020-12-22 18:30:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/784#issuecomment-749707751  

@mloskot  I will keep in mind the formatting of code snippets.  
I did not understand this:  
Please, consider the simplistic g++ 05_b_overlay_linestring_polygon_example.cpp that you execute: how compiler should know where to look for all the required headers?  
Please explain a little bit.

---

## Comment 5

> Username: mloskot  
> Created at: 2020-12-22 18:41:06 UTC  
> Updated at: 2020-12-22 18:42:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/784#issuecomment-749712128  

@GauravSarkar  
  
Sorry, I did not look at the (originally badly unformatted) input carefully.  
I assumed you are not compiling examples installed Boost (i.e. `/usr/include/boost`), but against headers in cloned repository (typical for a contributor).  
Hence, I misjudged your `g++ 05_b_overlay_linestring_polygon_example.cpp` (without `-I` option) must give errors about compiler not being able to find the headers, like here:  
  
```  
$  g++ 05_b_overlay_linestring_polygon_example.cpp  
05_b_overlay_linestring_polygon_example.cpp:22:10: fatal error: boost/foreach.hpp: No such file or directory  
   22 | #include <boost/foreach.hpp>  
      |          ^~~~~~~~~~~~~~~~~~~  
```  
  
Anyhow, the issue should now be fixed see https://github.com/boostorg/geometry/issues/784#issuecomment-749707716, for you too.

---

## Comment 6

> Username: GauravSarkar  
> Created at: 2020-12-22 18:49:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/784#issuecomment-749715581  

Thanks @mloskot  for clarifying.
