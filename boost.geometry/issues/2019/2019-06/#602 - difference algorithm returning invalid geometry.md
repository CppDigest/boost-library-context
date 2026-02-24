# #602 - difference algorithm returning invalid geometry [Open]

> Username: vschoech  
> Created at: 2019-06-21 09:43:37 UTC  
> Updated at: 2025-07-16 15:42:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/602  

My "tc::geo::polygon" type is actually a multi-polygon, using a polygon type that is based on **int**, oriented **counter-clockwise** and **open** (not closed). I am using **boost 1.70.0**. Please consider the following example:   
  
```C++  
tc::geo::polygon<int> polygonA;  
boost::geometry::read_wkt("MULTIPOLYGON(((529 3217,529 998,5337 998,5337 1834,5070 2000,5337 2072,5337 3475,529 3475,529 3312,1734 2054,2934 1670,3230 1690,2934 1400,1734 1784,529 3217),(4140 2582,5071 2001,4140 1754,3231 1691,4140 2582)))", polygonA); // does not throw  
boost::geometry::is_valid(polygonA); // returns true  
  
tc::geo::polygon<int> polygonB;  
boost::geometry::read_wkt("MULTIPOLYGON(((528 3218,528 2498,1734 1406,2556 1522,1734 1784,528 3218)),((4610 2288,5340 1178,5340 1832,4609 2289,4140 3002,2934 1574,2555 1521,2934 1400,4140 2582,4610 2288)))", polygonB); // does not throw  
boost::geometry::is_valid(polygonB); // returns true  
  
tc::geo::polygon<int> polygonC;  
boost::geometry::difference(polygonA, polygonB, polygonC); // does not throw  
// polygonC: MULTIPOLYGON(((2553 1521,1734 1406,529 2498,529 998,5337 998,5337 1183,4839 1939,4140 1754,3231 1691,4140 2582,3230 1690,2934 1400,2555 1521,2556 1522,2553 1521)),((1734 1784,529 3217,529 3216,1734 1784)),((3020 1676,4140 3002,4609 2289,5071 2001,5337 2072,5337 3475,529 3475,529 3312,1734 2054,2934 1670,3020 1676)))  
boost::geometry::is_valid(polygonC); // **** returns false! ****  
```  
  
The difference of two valid multi-polygons yields an invalid multi-polygon.  
  
See also:  
https://svn.boost.org/trac10/ticket/11674  
https://svn.boost.org/trac10/ticket/10661  
https://svn.boost.org/trac10/ticket/9768

---

## Comment 1

> Username: barendgehrels  
> Created at: 2019-06-26 10:11:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/602#issuecomment-505812088  

I have to postpone this issue until rescaling removal is finished. I know that in this usecase rescaling is not used, because integer coordinates are used, but the code changes to remove rescaling are broader than floating point only

---

## Comment 2

> Username: barendgehrels  
> Created at: 2019-06-26 10:13:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/602#issuecomment-505812458  

(Oops, sorry for the click on the wrong milestone )

---

## Comment 3

> Username: vschoech  
> Created at: 2020-03-09 10:18:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/602#issuecomment-596442962  

Confirmed that this issue is still present in boost 1.72.0. Looking forward to the next milestone!

---

## Comment 4

> Username: vschoech  
> Created at: 2020-06-17 09:42:50 UTC  
> Updated at: 2020-06-17 09:44:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/602#issuecomment-645270310  

Confirming that this issue is still present in boost 1.73.0. Looking forward to the next milestone!

---

## Comment 5

> Username: mloskot  
> Created at: 2021-02-15 08:31:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/602#issuecomment-779050558  

Moving this to milestone 1.76

---

## Comment 6

> Username: vschoech  
> Created at: 2021-03-26 16:57:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/602#issuecomment-808371695  

Confirming that this issue is still present in boost 1.75.0. Looking forward to the next milestone!

---

## Comment 7

> Username: vissarion  
> Created at: 2022-06-07 14:12:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/602#issuecomment-1148732368  

@vschoech is this still present in 1.79.0 (rescaling is removed). Sorry I cannot reproduce your issue because of the `tc::geo::polygon` type.

---

## Comment 8

> Username: vschoech  
> Created at: 2022-06-08 07:29:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/602#issuecomment-1149562898  

@vissarion Thank you for looking into this. I'll check as soon as we update our boost library (we'll wait for 1.80 because there will be another fix we need), and let you know.  
  
In the meantime, you can reproduce for yourself if you want. `tc::geo::polygon` isn't doing any magic. As described above, it is a multi-polygon, using a polygon type that is based on int, oriented counter-clockwise and open.

---

## Comment 9

> Username: vissarion  
> Created at: 2022-06-08 09:00:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/602#issuecomment-1149650416  

This is still present in 1.79.0.   
side note: for `double` instead of `int` the result is correct.

---

## Comment 10

> Username: vschoech  
> Created at: 2022-07-08 11:34:15 UTC  
> Updated at: 2022-07-08 11:34:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/602#issuecomment-1178883113  

> @vschoech is this still present in 1.79.0 (rescaling is removed).   
  
We updated our code base to 1.79 and I can now confirm that this issue is still present.

---

## Comment 11

> Username: vschoech  
> Created at: 2023-08-28 11:45:43 UTC  
> Updated at: 2023-08-28 13:42:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/602#issuecomment-1695551757  

:warning: Still reproducible in **boost 1.83.0**.  
  
The input values of our unit test have slightly changed, but I presume the error is still the same. The difference of two valid multi-polygons yields an invalid multi-polygon:  
  
```C++  
tc::geo::polygon<int> polygonA;  
boost::geometry::read_wkt("MULTIPOLYGON(((529 3313,1734 2054,2934 1670,3231 1691,4140 2582,5070 2001,5337 2071,5337 3475,529 3475,529 3313)),((529 3217,529 998,5337 998,5337 1834,5070 2001,4140 1754,3231 1691,2934 1400,1734 1784,529 3217)))", polygonA); // does not throw  
boost::geometry::is_valid(polygonA); // returns true  
  
tc::geo::polygon<int> polygonB;  
boost::geometry::read_wkt("MULTIPOLYGON(((528 3218,528 2498,1734 1406,2556 1522,1734 1784,528 3218)),((4610 2288,5340 1178,5340 1832,4609 2289,4140 3002,2934 1574,2555 1521,2934 1400,4140 2582,4610 2288)))", polygonB); // does not throw  
boost::geometry::is_valid(polygonB); // returns true  
  
tc::geo::polygon<int> polygonC;  
boost::geometry::difference(polygonA, polygonB, polygonC); // does not throw  
// polygonC: MULTIPOLYGON(((4608 2290,5070 2001,5337 2071,5337 3475,529 3475,529 3313,1734 2054,2934 1670,3020 1676,4140 3002,4608 2290)),((2554 1522,1734 1406,529 2497,529 998,5337 998,5337 1183,4839 1940,4140 1754,3231 1691,2934 1400,2555 1521,2556 1521,2554 1522),(4140 2582,3230 1690,3231 1691,4140 2582)),((5070 2001,5337 1833,5337 1834,5070 2001)))  
boost::geometry::is_valid(polygonC); // **** returns false! ****  
```

---

## Comment 12

> Username: vschoech  
> Created at: 2025-03-27 09:35:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/602#issuecomment-2757338609  

We updated our code base to boost 1.87.0 and I'm happy to confirm that I cannot reproduce this issue any more. ✔️
