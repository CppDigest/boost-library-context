# #742 - has_self_intersection segmentation fault [Closed]

> Username: marooncn  
> Created at: 2020-08-06 06:40:26 UTC  
> Updated at: 2021-05-12 10:32:13 UTC  
> Closed at: 2021-05-12 10:32:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/742  

Hi, I use _boost::geometry::difference_ to do difference operation,   
`boost::geometry::difference(polygon1, polygon2, output); `  
but it comes out segmentation fault:  
  
```  
>   [19] /lib/x86_64-linux-gnu/libc.so.6 : ()+0x354b0  
>   [18] /lib/x86_64-linux-gnu/libc.so.6 : gsignal()+0x38  
>   [17] /lib/x86_64-linux-gnu/libc.so.6 : abort()+0x16a  
>   [16] /usr/lib/x86_64-linux-gnu/libstdc++.so.6 : __gnu_cxx::__verbose_terminate_handler()+0x16d  
>     * ??:0  
>   [15] /usr/lib/x86_64-linux-gnu/libstdc++.so.6 : ()+0x8d6b6  
>   [14] /usr/lib/x86_64-linux-gnu/libstdc++.so.6 : ()+0x8d701  
>   [13] /usr/lib/x86_64-linux-gnu/libstdc++.so.6 : ()+0x8d919  
>   [12] planning : bool boost::geometry::detail::overlay::has_self_intersections<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, boost::geometry::detail::robust_policy<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, boost::geometry::model::point<long long, 2ul, boost::geometry::cs::cartesian>, double> >(boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator> const&, boost::geometry::detail::robust_policy<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, boost::geometry::model::point<long long, 2ul, boost::geometry::cs::cartesian>, double> const&, bool)+0x4a2  
>     * /usr/include/boost/geometry/algorithms/detail/has_self_intersections.hpp:116  
>   [11] planning() [0xde4e93]  
>   [10] planning : void boost::geometry::detail::overlay::traverse<false, true, boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, boost::geometry::detail::overlay::backtrack_check_self_intersections<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator> > >::apply<boost::geometry::detail::robust_policy<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, boost::geometry::model::point<long long, 2ul, boost::geometry::cs::cartesian>, double>, std::deque<boost::geometry::detail::overlay::traversal_turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, boost::geometry::segment_ratio<long long> >, std::allocator<boost::geometry::detail::overlay::traversal_turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, boost::geometry::segment_ratio<long long> > > >, std::deque<boost::geometry::model::ring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::allocator>, std::allocator<boost::geometry::model::ring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::allocator> > > >(boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator> const&, boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator> const&, boost::geometry::detail::overlay::operation_type, boost::geometry::detail::robust_policy<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, boost::geometry::model::point<long long, 2ul, boost::geometry::cs::cartesian>, double> const&, std::deque<boost::geometry::detail::overlay::traversal_turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, boost::geometry::segment_ratio<long long> >, std::allocator<boost::geometry::detail::overlay::traversal_turn_info<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, boost::geometry::segment_ratio<long long> > > >&, std::deque<boost::geometry::model::ring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::allocator>, std::allocator<boost::geometry::model::ring<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::allocator> > >&)+0xebf  
>     * /usr/include/boost/geometry/algorithms/detail/overlay/traverse.hpp:337 (discriminator 2)  
>   [9] planning : std::back_insert_iterator<std::vector<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, std::allocator<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator> > > > boost::geometry::detail::overlay::overlay<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, false, true, false, boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, (boost::geometry::overlay_type)2>::apply<boost::geometry::detail::robust_policy<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, boost::geometry::model::point<long long, 2ul, boost::geometry::cs::cartesian>, double>, std::back_insert_iterator<std::vector<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, std::allocator<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator> > > >, boost::geometry::strategy_intersection<boost::geometry::cartesian_tag, boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, boost::geometry::detail::robust_policy<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, boost::geometry::model::point<long long, 2ul, boost::geometry::cs::cartesian>, double>, void> >(boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator> const&, boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator> const&, boost::geometry::detail::robust_policy<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, boost::geometry::model::point<long long, 2ul, boost::geometry::cs::cartesian>, double> const&, std::back_insert_iterator<std::vector<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, std::allocator<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator> > > >, boost::geometry::strategy_intersection<boost::geometry::cartesian_tag, boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>, boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, boost::geometry::detail::robust_policy<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, boost::geometry::model::point<long long, 2ul, boost::geometry::cs::cartesian>, double>, void> const&)+0x50a  
>     * /usr/include/c++/5/bits/stl_tree.h:618  
>     *  (inlined by) /usr/include/c++/5/bits/stl_tree.h:589  
>     *  (inlined by) /usr/include/c++/5/bits/stl_tree.h:807  
>     *  (inlined by) /usr/include/c++/5/bits/stl_map.h:166  
>     *  (inlined by) /usr/include/boost/geometry/algorithms/detail/overlay/overlay.hpp:234  
>   [8] planning : adlab::planning::PriorWaypointGenerator::FilterObject()+0x403e  
>     * /usr/include/boost/geometry/algorithms/difference.hpp:68  
>     *  (inlined by) /usr/include/boost/geometry/algorithms/difference.hpp:114  
>     *  (inlined by) /usr/include/boost/geometry/algorithms/difference.hpp:160  
```  
  
I check the [document](https://www.boost.org/doc/libs/1_55_0/libs/geometry/doc/html/geometry/reference/concepts/concept_polygon.html#geometry.reference.concepts.concept_polygon.rules), and add the judgement:  
```  
if (is_valid(polygon1) && !has_self_intersections(polygon1)   
      && is_valid(polygon2) && !has_self_intersections(polygon2))  
    boost::geometry::difference(polygon1, polygon2, output);   
```  
but the error is still here. What's the problem?

---

## Comment 1

> Username: mloskot  
> Created at: 2020-08-06 08:32:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/742#issuecomment-669792029  

Please improve your report by adding  
- version of Boost you are using  
- a [minimal reproducible example](https://en.wikipedia.org/wiki/Minimal_working_example) in C++

---

## Comment 2

> Username: marooncn  
> Created at: 2020-08-06 13:48:34 UTC  
> Updated at: 2020-08-06 13:48:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/742#issuecomment-669937672  

The ploygon outer points are:  
```  
139.626 35.979  
139.251 36.1352  
139.157 36.2602  
139.016 37.0218  
138.72 36.7602  
138.689 36.7602  
138.626 36.8227  
138.626 37.4165  
138.439 37.3227  
138.282 37.2915  
137.72 37.729  
137.501 38.0415  
137.501 38.1352  
137.251 38.0727  
137.126 38.0727  
136.126 39.1665  
136.001 39.479  
136.11 39.9014  
136.173 39.9639  
137.251 39.479  
137.97 38.7915  
137.97 38.734  
138.72 38.854  
138.814 38.8227  
139.126 38.604  
139.47 38.2602  
139.47 38.2337  
140.001 38.2602  
140.001 38.1352  
139.72 37.7602  
139.416 37.4156  
140.626 37.2915  
141.001 36.9165  
141.001 36.0102  
140.97 35.979  
140.107 35.979  
140.376 35.979  
140.376 35.979  
141.001 35.979  
141.001 35.479  
140.876 35.354  
140.251 35.229  
140.189 35.479  
140.245 35.8199  
140.189 35.7915  
139.626 35.9477  
139.626 35.979  
```  
you can assign these points to _polygon1_, I found the segmentation fault is caused by _has_self_intersections(polygon1)_ and this operation probably be called by _difference_ function, so there comes an core dump.

---

## Comment 3

> Username: marooncn  
> Created at: 2020-08-06 13:51:23 UTC  
> Updated at: 2020-08-06 13:51:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/742#issuecomment-669939360  

this error can be solved by calling _convex_hull_ function. But I think it's a bug of _has_self_intersections_, it can output _false_ rather than throw a segmentation fault.

---

## Comment 4

> Username: awulkiew  
> Created at: 2020-08-06 14:14:40 UTC  
> Updated at: 2020-08-06 14:28:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/742#issuecomment-669954095  

We have to be able to reproduce the problem. For this we have to know how do you do it exactly. We need:  
- version of Boost  
- version of compiler and compiler options/flags  
- the exact code causing the problem  
  
Do you use `boost::geometry::model::point` and `boost::geometry::model::polygon` from Boost.Geometry or do you use your own geometries?  
  
EDIT: ok I see that the following types are used in the function call:  
- `boost::geometry::model::point<long long, 2ul, boost::geometry::cs::cartesian>` (this is probably internally used RobustPoint type)  
- `boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>`  
- `boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double, boost::geometry::cs::cartesian>, true, true, std::vector, std::vector, std::allocator, std::allocator>`

---

## Comment 5

> Username: marooncn  
> Created at: 2020-08-07 03:43:04 UTC  
> Updated at: 2020-08-07 08:36:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/742#issuecomment-670304807  

@awulkiew @mloskot   
  
version of Boost:   
  
> 1.58  
  
version of compiler and compiler options/flags:   
> cmake_minimum_required(VERSION 3.5.1)  
> project(boost_test)  
> set(CMAKE_CXX_STANDARD 11)  
  
the exact code causing the problem  
```  
#include <iostream>  
#include <vector>  
#include <fstream>   
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
  
using boost::geometry::detail::overlay::has_self_intersections;  
using boost::geometry::is_valid;  
typedef boost::geometry::model::d2::point_xy<double> boost_point;  
typedef boost::geometry::model::polygon<boost_point> boost_polygon;  
  
  
int main(int argc, char ** argv) {  
  std::vector<boost_point> points;  
  // the path of txt file containing the list points above  
  std::string txt_name =  "/home/administrator/test.txt";    
  std::fstream f(txt_name, std::ios::in);  
  if (!f.is_open())  
  {  
    std::cout  << txt_name << " doesn't exist" << std::endl;  
    return -1;  
  }  
  double x, y;  
  while (!f.eof()) {  
    f >> x >> y;  
    points.push_back(boost_point(x, y));  
  }  
  f.close();  
  
  
  boost_polygon polygon1, polygon_hull;  
  boost::geometry::assign_points(polygon1, points);  
  //  uncomment here, it will be OK  
  //boost::geometry::convex_hull(polygon1, polygon_hull);  
  //polygon1 = polygon_hull;    
  
  std::cout<< is_valid(polygon1) <<std::endl;  
  // segmentation because of has_self_intersections, why does it occur core dump rather than output false?  
  std::cout<< has_self_intersections(polygon1) <<std::endl;  
  
  return 0;  
}  
```  
It will cause segmentation fault.  
And when you uncomment _convex_hull_, it will be OK.

---

## Comment 6

> Username: mloskot  
> Created at: 2020-08-07 07:17:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/742#issuecomment-670369528  

@marooncn An obvious suggestion is to try the latest Boost

---

## Comment 7

> Username: marooncn  
> Created at: 2020-08-07 07:44:14 UTC  
> Updated at: 2020-08-07 07:59:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/742#issuecomment-670380178  

OK, thank you. The bug seems fixed in the latest version. To avoid this bug in the old version, calling _convex_hull_ before might be a good choice.

---

## Comment 8

> Username: mloskot  
> Created at: 2020-08-07 07:46:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/742#issuecomment-670380958  

@marooncn Thanks for the confirmation.   
Since Boost does not publish bugfix releases, there won't be any Boost 1.58.1.   
  
@awulkiew  I think this can be closed

---

## Comment 9

> Username: marooncn  
> Created at: 2020-08-07 08:39:50 UTC  
> Updated at: 2020-08-07 08:40:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/742#issuecomment-670403854  

@mloskot  Actually not, I thought you have tested with the latest boost version before. I just update boost to v1.73 and excute above code and the segmentation fault is still here.

---

## Comment 10

> Username: marooncn  
> Created at: 2020-08-07 08:49:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/742#issuecomment-670408059  

Add info:   
I drew the polygon  
![image](https://user-images.githubusercontent.com/29606478/89627582-aff22a00-d8cd-11ea-901d-645657e7840c.png)

---

## Comment 11

> Username: awulkiew  
> Created at: 2020-08-07 16:14:06 UTC  
> Updated at: 2020-08-07 16:14:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/742#issuecomment-670594560  

I tried to reproduce it with VS2017. The function throws `overlay_invalid_input_exception` which is not caught. In your output there is `__gnu_cxx::__verbose_terminate_handler()` which suggests termination of a program after unhandled exception is thrown. The output that you pasted is not full. Can you see something like `terminate called after throwing an instance of ...` before the part you pasted?  
  
`is_valid()` returns `false` and as you can see there is a spike in the lower right corner.  
  
After calling `boost::geometry::remove_spikes(polygon1)` the input is OK and the exception is not thrown.  
  
--------  
  
Regarding `has_self_intersections()`. This is a function from `namespace boost::geometry::detail` so it's for internal use. As you can see here:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/has_self_intersections.hpp#L140  
one of the parameters is `throw_on_self_intersection` defaulted to `true`. So the throwing is intended. I agree though that it should probably be called differently, e.g. `throw_on_self_intersection()`. Still there is no bug here.  
  
If you want to see the reason why a geometry is invalid you can get it from `is_valid()`:  
https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/algorithms/is_valid/is_valid_2_with_message.html  
  
--------  
  
So:  
- make the input geometries valid before passing them into algorithms,  
- catch exceptions.  
  
Let me know if I'm missing something.

---

## Comment 12

> Username: marooncn  
> Created at: 2020-08-09 04:41:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/742#issuecomment-671005603  

@awulkiew Thanks for your detailed reply. Actually at first I use _is_valid_ to check polygons validity:  
```  
if (is_valid(polygon1) && is_valid(polygon2))  
    boost::geometry::difference(polygon1, polygon2, output);   
```  
But when it executes, it occurs the segmentation fault in _difference_ function. So I add _has_self_intersections_ to judge. But the segmentation fault is still here when executing _difference_. I think _is_valid_ doesn't totally prevent invalid polygon to do _difference_.   
And as you said, the default value of _throw_on_self_intersection_ is true, so _difference_ occurs segmentation default and  if the default value can be set to false to make the program run or if the document can illustrate this because I really stop here for a week.

---

## Comment 13

> Username: awulkiew  
> Created at: 2020-08-09 11:08:35 UTC  
> Updated at: 2020-08-09 11:16:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/742#issuecomment-671038251  

> I think is_valid doesn't totally prevent invalid polygon to do difference.  
  
This is possible. However I checked the polygon that you shared above and `is_valid` returns `false` so everything is ok, at least with VS2017.  
Are the points above the ones that caused the initial problem with difference or was this a different polygon?  
Do you get `true` when you call `is_valid` for this polygon?  
  
So I can try to investigate this but I need a minimal working example. Your example was for `has_self_intersections` not `difference`. I also need exact these 2 polygons which are causing the problem.  
  
> segmentation fault  
  
This is caused by the fact that you didn't catch the exception. I ask again, can you see this message:  
```terminate called after throwing an instance of ...```  
in the output?  
  
If you can then the termination of a program is because you didn't catch the exception. Put everything in a try-catch block and see if the problem is still there.

---

## Comment 14

> Username: marooncn  
> Created at: 2020-08-09 16:17:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/742#issuecomment-671071246  

@awulkiew Yes, I capture the polygon that made _is_valid_ true and made _difference_ function segmentation fault  to do offline test, as you said, it also returned false in offline test for _is_valid_. It's weird.

---

## Comment 15

> Username: barendgehrels  
> Created at: 2021-05-12 10:32:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/742#issuecomment-839663922  

As @awulkiew said, if you add `false` in `has_self_intersections` (which is a function in detail and not supposed to be called from this code), no exception will be thrown. Alternatively you can catch the exception. It is thrown on purpose.  
  
Output will be:  
```  
valid: 0  
self intersections: 1  
```  
  
I reworked the file a bit because there are other arguments now in that function  
  
https://godbolt.org/z/z3oYd7Gze  
  
But as you explained, you don't call it directly but it is called from `difference` and that throws. You can (at this moment) avoid that by setting `BOOST_GEOMETRY_OVERLAY_NO_THROW`  
  
Closing this issue.
