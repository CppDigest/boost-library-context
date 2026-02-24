# #1288 - Difference or intersection fail depending on BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE [Closed]

> Username: MauriceHubain  
> Created at: 2024-06-24 12:51:15 UTC  
> Updated at: 2024-11-25 13:12:18 UTC  
> Closed at: 2024-11-25 13:12:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/1288  

Depending on if we set BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE, either difference or intersection fail for some of our test scenarios.  
  
The test case for difference:  
  
MULTIPOLYGON(((-2.0 -1.5, 2.0 -1.5, 2.0 1.5, -2.0 1.5)))  
POLYGON((-0.5 -1.49999999, -2.0 -0.1, -1.99999999 -1.5))  
  
difference(MULTIPOLYGON, POLYGON, result)  
  
result should be like MULTIPOLYGON(((-2.0 -0.1, -0.5 -1.49999999, 2.0 -1.5, 2.0 1.5, -2.0 1.5)))  
  
If we do not define BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE, result is empty.  
If we define BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE, we get the expected result.  
  
  
The test case for intersection:  
  
MULTIPOLYGON(((1.0 2.0, 3.0 1.0, 4.0 3.0, 2.0 4.0)))  
POLYGON((2.0 1.5, 3.0 1.5, 3.0 2.5, 2.0 2.5))  
  
intersection(MULTIPOLYGON, POLYGON, result)  
  
result should be like MULTIPOLYGON(((2.0 1.5, 3.0 1.5, 3.0 2.5, 2.0 2.5))), which is similar to the input POLYGON.  
  
If we do not define BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE, we get the expected result.  
If we define BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE, result contains the bigger input MULTIPOLYGON.  
  
  
Attached BoostGeometryTests.cpp contains these 2 test cases and some more, which can be enabled by defining ALL_TEST_CASES 1 (in the second line).  
[BoostGeometryTests.cpp.txt](https://github.com/user-attachments/files/15955912/BoostGeometryTests.cpp.txt)  
  
With Boost 1.71.0 all test cases succeed (in fact all but Intersection_04c1, where Intersection_04c2 is the same test with slightly adapted expectation - 5 points instead of 4).  
With Boost 1.80.0 to 1.85.0 multiple tests fail (different, depending on the BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE flag).  
  
It is not important, that we get exactly the declared expected results, but reasonable ones.

---

## Comment 1

> Username: vissarion  
> Created at: 2024-06-26 18:04:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/1288#issuecomment-2192322819  

Thanks for the detailed examples and code. I can reproduce it. Note that `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` is deprecated, and will be removed (probably in the version so we focus on issues without setting this flag. And there are quite a few. At a first look this does not seems as a numerical issue since your instances are failing even with multiprecision arithmetic. Need to look at it in more detail.

---

## Comment 2

> Username: MauriceHubain  
> Created at: 2024-06-27 10:59:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/1288#issuecomment-2194388139  

> Note that BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE is deprecated, and will be removed  
  
This is definitely fine. We just tried that flag, because it seemed to fix our problems with the difference function.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2024-07-28 09:03:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/1288#issuecomment-2254404070  

### First  
Can you next time please provide a minimal scenario? 537 lines is too much to analyze  
  
### Second  
Herewith an example of such a scenario for the case you list in the description. I **cannot** reproduce it with Boost 1.85 nor with Boost 1.84  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
#include <iostream>  
#include <iomanip>  
  
namespace bg = boost::geometry;  
  
template <typename T>  
void test()  
{  
    using point_t = bg::model::point<T, 2, bg::cs::cartesian>;  
    using polygon_t = bg::model::polygon<point_t, false, false>;  
    using mp_t = bg::model::multi_polygon<polygon_t>;  
  
    mp_t poly1;  
    bg::read_wkt("MULTIPOLYGON(((-2.0 -1.5, 2.0 -1.5, 2.0 1.5, -2.0 1.5)))", poly1);  
    bg::correct(poly1);  
  
    mp_t poly2;  
    bg::read_wkt("MULTIPOLYGON(((-0.5 -1.49999999, -2.0 -0.1, -1.99999999 -1.5)))", poly2);     
    bg::correct(poly2);  
  
    mp_t diff, inters;  
    bg::difference(poly1, poly2, diff);  
    bg::intersection(poly1, poly2, inters);  
  
    std::cout << std::setprecision(12) << std::boolalpha;  
    std::cout   
        << "Type: " << typeid(T).name()  
        << " a: " << bg::area(poly1)  
        << " b: " << bg::area(poly2)  
        << " diff: " << bg::area(diff)  
        << " ints: " << bg::area(inters)  
        << " a-b: " << bg::area(poly1) - bg::area(poly2)  
        << " d+i " << bg::area(diff) + bg::area(inters)  
        << " validity diff: " << bg::is_valid(diff)  
        << " ints: " << bg::is_valid(inters)  
        << std::endl;  
}  
  
int main() {  
    test<float>();  
    test<double>();  
    test<long double>();  
}  
  
```  
  
### My output in all cases  
```  
Type: f a: 12 b: 1.04999999888 diff: 10.9500000179 ints: 1.04999999888 a-b: 10.9500000011 d+i 12.0000000168 validity diff: true ints: true  
Type: d a: 12 b: 1.049999993 diff: 10.95 ints: 1.05 a-b: 10.950000007 d+i 12 validity diff: true ints: true  
Type: e a: 12 b: 1.049999993 diff: 10.95 ints: 1.05 a-b: 10.950000007 d+i 12 validity diff: true ints: true  
```  
  
Did you call "correct"?  
Can we close it or replace it with a minimal example which can reproduce?

---

## Comment 4

> Username: barendgehrels  
> Created at: 2024-07-28 12:15:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1288#issuecomment-2254497848  

BTW the reason that I checked it again is that I have a concept fix for another recent issue and wanted to check if it would fix this as well.

---

## Comment 5

> Username: MauriceHubain  
> Created at: 2024-07-29 11:08:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/1288#issuecomment-2255643592  

If you change your example in a way, that poly2 is a polygon, not a multipolygon (as in our test cases), the test fails for double and long double precision:  
  
[...]  
  polygon_t poly2;  
  bg::read_wkt("POLYGON((-0.5 -1.49999999, -2.0 -0.1, -1.99999999 -1.5))", poly2);  
  bg::correct(poly2);  
[...]  
  
Output:  
  
Type: float a: 12 b: 1.04999999888 diff: 10.9500000179 ints: 1.04999999888 a-b: 10.9500000011 d+i 12.0000000168 validity diff: true ints: true  
Type: double a: 12 b: 1.049999993 diff: 0 ints: 1.049999993 a-b: 10.950000007 d+i 1.049999993 validity diff: true ints: true  
Type: long double a: 12 b: 1.049999993 diff: 0 ints: 1.049999993 a-b: 10.950000007 d+i 1.049999993 validity diff: true ints: true  
  
So at least there is something wrong with this combination of input geometries.  
In accordance to the documentation "Behavior: areal (e.g. polygon) - All combinations of: box, ring, polygon, multi_polygon", I would expect, that this combination should be supported.

---

## Comment 6

> Username: MauriceHubain  
> Created at: 2024-07-29 11:08:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/1288#issuecomment-2255644532  

Nevertheless, I will check, if changing both input geometries to multipolygon, would solve all our issues.

---

## Comment 7

> Username: barendgehrels  
> Created at: 2024-07-29 13:38:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/1288#issuecomment-2255977584  

That should indeed not be necessary, poly should give the same result as multi poly

---

## Comment 8

> Username: MauriceHubain  
> Created at: 2024-07-29 13:48:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/1288#issuecomment-2256000705  

Unfortunately I found a first example (from our "real life" issues), where also using mutlipolygon  does not give the expected results:  
  
  mp_t poly1;  
  bg::read_wkt("MULTIPOLYGON(((1.2549999979079400 0.85000000411847698, -1.2550000020920500 0.84999999897038103, -1.2549999999999999 -0.85000000102961903, 1.2549999999999999 -0.84999999999999998)))", poly1);  
  bg::correct(poly1);  
  
  mp_t poly2;  
  bg::read_wkt("MULTIPOLYGON(((-0.87500000000000000 -0.84999999999999998, -0.87500000000000000 -0.070000000000000201, -1.2549999999999999 -0.070000000000000201, -1.2549999999999999 -0.84999999999999998)))", poly2);  
  bg::correct(poly2);  
  
Output:  
Type: float a: 4.26700010347 b: 0.296400005227 diff: 3.97060009825 ints: 0.296400005227 a-b: 3.97060009825 d+i 4.26700010347 validity diff: true ints: true  
Type: double a: 4.26700000517 b: 0.2964 diff: 0 ints: 0.2964 a-b: 3.97060000517 d+i 0.2964 validity diff: true ints: true  
Type: long double a: 4.26700000517 b: 0.2964 diff: 0 ints: 0.2964 a-b: 3.97060000517 d+i 0.2964 validity diff: true ints: true

---

## Comment 9

> Username: barendgehrels  
> Created at: 2024-07-29 13:49:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/1288#issuecomment-2256002315  

> That should indeed not be necessary, poly should give the same result as multi poly  
  
and I can reproduce it so it's labeled as a bug now

---

## Comment 10

> Username: barendgehrels  
> Created at: 2024-07-29 13:50:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/1288#issuecomment-2256003680  

> Unfortunately I found a first example (from our "real life" issues), where also using mutlipolygon does not give the expected results:  
>   
> mp_t poly1; bg::read_wkt("MULTIPOLYGON(((1.2549999979079400 0.85000000411847698, -1.2550000020920500 0.84999999897038103, -1.2549999999999999 -0.85000000102961903, 1.2549999999999999 -0.84999999999999998)))", poly1); bg::correct(poly1);  
>   
> mp_t poly2; bg::read_wkt("MULTIPOLYGON(((-0.87500000000000000 -0.84999999999999998, -0.87500000000000000 -0.070000000000000201, -1.2549999999999999 -0.070000000000000201, -1.2549999999999999 -0.84999999999999998)))", poly2); bg::correct(poly2);  
>   
> Output: Type: float a: 4.26700010347 b: 0.296400005227 diff: 3.97060009825 ints: 0.296400005227 a-b: 3.97060009825 d+i 4.26700010347 validity diff: true ints: true Type: double a: 4.26700000517 b: 0.2964 diff: 0 ints: 0.2964 a-b: 3.97060000517 d+i 0.2964 validity diff: true ints: true Type: long double a: 4.26700000517 b: 0.2964 diff: 0 ints: 0.2964 a-b: 3.97060000517 d+i 0.2964 validity diff: true ints: true  
  
Can you please file other failing cases under new tickets?  
Thanks!

---

## Comment 11

> Username: MauriceHubain  
> Created at: 2024-07-29 13:53:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/1288#issuecomment-2256011472  

Sure, I can file a new ticket.  
But this scenario was already part of the provided test bench.

---

## Comment 12

> Username: barendgehrels  
> Created at: 2024-07-29 13:59:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/1288#issuecomment-2256026116  

> Sure, I can file a new ticket. But this scenario was already part of the provided test bench.  
  
We can really not work with such a bench, sorry. Because things are fixed one by one, there can (sometimes) be months in between and handled by different persons.  
  
So if one is fixed - the ticket should be closed.

---

## Comment 13

> Username: MauriceHubain  
> Created at: 2024-07-29 14:05:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/1288#issuecomment-2256040728  

> We can really not work with such a bench, sorry. Because things are fixed one by one, there can (sometimes) be months in between and handled by different persons.  
>   
> So if one is fixed - the ticket should be closed.  
  
Ok, that's fine.  
  
I filed a new ticket https://github.com/boostorg/geometry/issues/1299

---

## Comment 14

> Username: barendgehrels  
> Created at: 2024-11-15 21:49:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/1288#issuecomment-2479988944  

The status is still the same, and weird, unfortunately.  
Only if the second is a polygon, the difference (a-b) fails (its area is `0.0` instead of `10.95`)  
Intersection is OK (at least on current branch)  
  
|    | Poly 2 | Multi 2 |  
| -------- | ------- | ------- |  
| **Poly 1** | succeeds    | succeeds    |  
| **Multi 1** | fails     | succeeds     |
