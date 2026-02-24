# #629 - MSVC Warning: C4244 'initializing': conversion from 'CoordinateType' to 'const PromotedType', possible loss of data [Closed]

> Username: AlastairHolmes  
> Created at: 2019-10-24 11:28:22 UTC  
> Updated at: 2025-03-27 10:15:10 UTC  
> Closed at: 2025-03-18 13:06:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/629  

Minimal Code to produce related warning (boost 1.70.0 MSVC 2017 15.8.0 /std:c++latest /W4):  
  
```  
boost::geometry::model::polygon<  
    boost::geometry::model::point<std::int64_t, 3, boost::geometry::cs::cartesian>  
> test;  
boost::geometry::remove_spikes(test);  
```  
  
If the `std::int64_t` in the above is changed to `int` there is no warning.  
  
On line 245 (side_by_triangle.hpp) boost decides the type of PromotedType = `select_most_precise<double,std::int64_t>` => `double`. And then initializes PromotedType values with `std::int64_t` values.  
  
This pattern of select_most_precise<double,...> is pretty common, and I have found the same warning being produced in other parts of boost that do the same thing when using with `std::int64_t`.

---

## Comment 1

> Username: vschoech  
> Created at: 2020-03-09 10:03:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/629#issuecomment-596436310  

This is still a problem in boost 1.72.0.  
`\boost_1_72_0\boost\geometry\strategies\cartesian\side_by_triangle.hpp(123): error C4244: 'initializing': conversion from 'CoordinateType' to 'PromotedType', possible loss of data`

---

## Comment 2

> Username: vschoech  
> Created at: 2020-06-17 09:12:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/629#issuecomment-645255470  

This is still a problem in boost 1.73.0.  
`\boost_1_73_0\boost\geometry\strategies\cartesian\side_by_triangle.hpp(123): error C4244: 'initializing': conversion from 'CoordinateType' to 'PromotedType', possible loss of data`

---

## Comment 3

> Username: mloskot  
> Created at: 2020-06-18 15:00:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/629#issuecomment-646076689  

I think it should be safe to `... = static_cast<PromotedType>(...)` wherever the left-hand side is an explicitly promoted type.  
  
@awulkiew What do you think?

---

## Comment 4

> Username: Ne94fets  
> Created at: 2020-06-21 14:37:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/629#issuecomment-647136655  

Hello, I have a similar problem on Windows using msvc14.1. On linux there is no warning by the way.  
  
I created a minimal example:  
```  
#include <boost/geometry.hpp>  
  
namespace bg = boost::geometry;  
typedef bg::model::polygon<bg::model::d2::point_xy<double>> polygon_t;  
typedef bg::model::multi_polygon<polygon_t> mpolygon_t;  
  
int main() {  
    polygon_t p0{ {{-5,-5},{-5,5},{5,5},{5,-5},{-5,-5}} };  
    polygon_t p1{ {{-4,-4},{-4,4},{4,4},{4,-4},{-4,-4}} };  
    mpolygon_t out;  
  
    bg::difference(p0, p1, out);  
}  
```  
  
I than tracked it back to `point_is_spike_or_equal.hpp` line 142-146 and added the following lines to get some debugging output:  
```  
static_assert(Point1::foo);  
static_assert(robust_point_type::foo);  
```  
  
And got the following errors:  
```  
1>...\boost\geometry\algorithms\detail\point_is_spike_or_equal.hpp(148): error C2039: 'foo': is not a member of 'boost::geometry::model::d2::point_xy<double,boost::geometry::cs::cartesian>'  
1>...\consoleapplication1.cpp(9): note: see declaration of 'boost::geometry::model::d2::point_xy<double,boost::geometry::cs::cartesian>'  
1>...\boost\geometry\algorithms\detail\point_is_spike_or_equal.hpp(148): error C2065: 'foo': undeclared identifier  
1>...\boost\geometry\algorithms\detail\point_is_spike_or_equal.hpp(149): error C2039: 'foo': is not a member of 'boost::geometry::model::point<__int64,2,boost::geometry::cs::cartesian>'  
1>...\boost\geometry\policies\robustness\get_rescale_policy.hpp(188): note: see declaration of 'boost::geometry::model::point<__int64,2,boost::geometry::cs::cartesian>'  
1>...\boost\geometry\algorithms\detail\point_is_spike_or_equal.hpp(149): error C2065: 'foo': undeclared identifier  
```  
  
Where the 4th line is the interesting one. Why is the `coordinate_type` of `boost::geometry::model::point` an `__int64`. `robust_point_type::type` should have the same type as `Point1` in the 2nd line.

---

## Comment 5

> Username: vschoech  
> Created at: 2021-03-26 14:24:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/629#issuecomment-808260960  

This is still a problem in 1.75.0.  
`\boost_1_75_0\boost\geometry\strategies\cartesian\side_by_triangle.hpp(123,37): error C4244: 'initializing': conversion from 'CoordinateType' to 'PromotedType', possible loss of data  
`

---

## Comment 6

> Username: wanghan02  
> Created at: 2022-07-06 09:26:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/629#issuecomment-1175995783  

This is still a problem in 1.79.0.  
`\boost_1_79_0\boost\geometry\strategy\cartesian\side_by_triangle.hpp(102,37): warning C4244: 'initializing': conversion from 'CoordinateType' to 'PromotedType', possible loss of data`

---

## Comment 7

> Username: yoavmil  
> Created at: 2022-07-06 12:38:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/629#issuecomment-1176171776  

Seems unmaintained.

---

## Comment 8

> Username: wanghan02  
> Created at: 2023-08-28 12:36:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/629#issuecomment-1695623561  

This is still a problem in 1.83.0.  
`boost_1_83_0\boost\geometry\strategy\cartesian\side_by_triangle.hpp(103,37): error C4244: 'initializing': conversion from 'CoordinateType' to 'PromotedType', possible loss of data`

---

## Comment 9

> Username: leewi9  
> Created at: 2024-03-05 15:59:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/629#issuecomment-1979103131  

still problem in 1.84.0  
  
boost_1_84_0\boost\range\irange.hpp(168,32): warning C4244: 'return': conversion from '__int64' to 'int', possible loss of data

---

## Comment 10

> Username: wanghan02  
> Created at: 2025-03-04 12:54:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/629#issuecomment-2697478451  

still problem in 1.87.0  
  
boost_1_87_0\boost\geometry\strategy\cartesian\side_by_triangle.hpp(106,36): warning C4244: 'initializing': conversion from 'boost::geometry::strategy::side::side_by_triangle<CalculationType>::apply::coor_t' to 'boost::geometry::strategy::side::side_by_triangle<CalculationType>::apply::promoted_t', possible loss of data

---

## Comment 11

> Username: barendgehrels  
> Created at: 2025-03-16 10:34:13 UTC  
> Updated at: 2025-03-16 10:46:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/629#issuecomment-2727323069  

Updated scenario (based on the lines in the report):  
```  
#include <boost/geometry.hpp>  
#include <iostream>  
  
template <typename T>  
void display_promotion()  
{  
    using promoted_t = typename boost::geometry::promote_integral<T>::type;  
    std::cout << sizeof(T) << " -> " << sizeof(promoted_t) << std::endl;  
    // The following does not link for __int128 (or its boost version)  
    std::cout << "t: " << typeid(T).name() << " -> promoted_t: " << typeid(promoted_t).name() << std::endl;  
}  
  
int main()  
{  
    using coor_t = std::int64_t;  
    using point_t = boost::geometry::model::point<coor_t, 2, boost::geometry::cs::cartesian>;  
    boost::geometry::model::ring<point_t> test  
        {  
            {0, 0},  
            {0, 1},  
            {1, 1},  
            {2, 1},  
            {1, 1},  
            {1, 0},  
            {0, 0}  
        };  
    std::cout << "Input: " << boost::geometry::wkt(test) << std::endl;  
    boost::geometry::remove_spikes(test);  
    std::cout << "Result: " << boost::geometry::wkt(test) << std::endl;  
  
    display_promotion<short>();  
    display_promotion<int>();  
    display_promotion<long>();  
    display_promotion<long long>();  
    display_promotion<std::int64_t>();  
    // display_promotion<boost::int128_type>();  
    return 0;  
  
}  
  
```

---

## Comment 12

> Username: barendgehrels  
> Created at: 2025-03-16 11:07:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/629#issuecomment-2727335662  

>     // display_promotion<boost::int128_type>();  
  
Side note (also for myself), it's a known problem  
https://github.com/llvm/llvm-project/issues/119608  
  
on Mac with   
you can still compile adding ` -lc++abi` for both GCC and Clang  
  
And you will get  
```  
16 -> 16  
t: __int128 -> promoted_t: __int128  
```

---

## Comment 13

> Username: barendgehrels  
> Created at: 2025-03-16 11:09:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/629#issuecomment-2727336173  

Another link (slightly off-topic now)  
https://quuxplusone.github.io/blog/2019/02/28/is-int128-integral/

---

## Comment 14

> Username: vschoech  
> Created at: 2025-03-27 10:15:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/629#issuecomment-2757492423  

⚠️ This still is a problem in boost 1.87.0, compiling with MSVC 2022 Version 17.13.1:  
  
```  
3>C:\Libraries\boost_1_87_0\boost\geometry\strategy\cartesian\side_by_triangle.hpp(103,37): error C4244: 'initializing': conversion from 'boost::geometry::strategy::side::side_by_triangle<CalculationType>::apply::coor_t' to 'boost::geometry::strategy::side::side_by_triangle<CalculationType>::apply::promoted_t', possible loss of data  
3>C:\Libraries\boost_1_87_0\boost\geometry\strategy\cartesian\side_by_triangle.hpp(103,37): error C4244:         with  
3>C:\Libraries\boost_1_87_0\boost\geometry\strategy\cartesian\side_by_triangle.hpp(103,37): error C4244:         [  
3>C:\Libraries\boost_1_87_0\boost\geometry\strategy\cartesian\side_by_triangle.hpp(103,37): error C4244:             CalculationType=void  
3>C:\Libraries\boost_1_87_0\boost\geometry\strategy\cartesian\side_by_triangle.hpp(103,37): error C4244:         ]  
```
