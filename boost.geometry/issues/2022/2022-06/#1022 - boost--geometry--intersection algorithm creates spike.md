# #1022 - boost::geometry::intersection algorithm creates spike [Closed]

> Username: vschoech  
> Created at: 2022-06-21 14:04:07 UTC  
> Updated at: 2022-07-13 08:35:52 UTC  
> Closed at: 2022-07-13 08:35:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1022  

My `tc::geo::polygon<int>` type is actually a multi-polygon, using a polygon type that is based on **int**, oriented **counter-clockwise** and **open** (not closed). I am using **boost 1.75.0**.  
  
Please consider the following example as it occurred in our code:   
  
```C++  
tc::geo::polygon<int> polygonA;  
boost::geometry::read_wkt("MULTIPOLYGON(((2029 464,3829 464,3829 618,3754 1044,3695 1306,3647 1483,3607 1610,3571 1706,3540 1781,3511 1842,3485 1891,3461 1933,3438 1968,3416 1999,3395 2026,3375 2049,3355 2070,3335 2089,3316 2107,3297 2122,3278 2137,3258 2150,3238 2163,3217 2174,3195 2185,3172 2195,3148 2205,3122 2214,3092 2223,3059 2231,3021 2239,2973 2246,2911 2253,2815 2259,2029 2264,2029 464)))", polygonA);  
tc::geo::polygon<int> polygonB;  
boost::geometry::read_wkt("MULTIPOLYGON(((2029 2264,2584 2262,2728 2259,2830 2256,2910 2252,2978 2248,3038 2244,3092 2239,3140 2234,3185 2229,3227 2224,3266 2218,3303 2212,3338 2206,3372 2200,3404 2194,3435 2187,3464 2181,3493 2174,3521 2167,3548 2160,3574 2152,3599 2145,3624 2137,3649 2129,3673 2121,3696 2113,3719 2104,3742 2095,3764 2086,3786 2077,3808 2068,3829 2058,3829 2264,2029 2264)))", polygonB);  
tc::geo::polygon<int> polygonC;  
boost::geometry::intersection(polygonA, polygonB, polygonC);  
tc::geo::polygon<int> polygonD = polygonC;  
boost::geometry::remove_spikes(polygonD);  
// polygonC=polygon(MULTIPOLYGON(((2690 2259,2728 2259,2830 2256,2910 2252,2930 2251,2911 2253,2815 2259,2690 2259))))  
// polygonD=polygon(MULTIPOLYGON(((2728 2259,2830 2256,2910 2252,2930 2251,2911 2253,2815 2259,2728 2259))))  
```  
  
Alternatively, consider the following simplified example:   
  
```C++  
tc::geo::polygon<int> polygonA;  
boost::geometry::read_wkt("MULTIPOLYGON(((3829 618, 2815 2259, 2029 2264, 3829 618)))", polygonA);  
tc::geo::polygon<int> polygonB;  
boost::geometry::read_wkt("MULTIPOLYGON(((2029 2264, 2584 2262, 2728 2259, 3829 2058, 3829 2264, 2029 2264)))", polygonB);  
tc::geo::polygon<int> polygonC;  
boost::geometry::intersection(polygonA, polygonB, polygonC);  
tc::geo::polygon<int> polygonD = polygonC;  
boost::geometry::remove_spikes(polygonD);  
// polygonC: MULTIPOLYGON(((2690 2259,2728 2259,2827 2241,2815 2259,2690 2259)))  
// polygonD: MULTIPOLYGON(((2728 2259,2827 2241,2815 2259,2728 2259)))  
```  
  
It was my expectation that when the input does not contain spikes, neither does the output from boost::geometry::intersection. Please clarify whether my assumption is wrong or there is a bug in boost::geometry::intersection.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2022-06-22 16:20:52 UTC  
> Updated at: 2022-06-22 16:22:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/1022#issuecomment-1163333056  

Hi @vschoech ,  
  
Thanks for the report! I'm trying to reproduce it, but without success.  
  
The output of the first case is:  
![image](https://user-images.githubusercontent.com/334849/175081975-589a92ab-9a60-44d1-85bf-1fefa67b3ddd.png)  
  
This is zoomed in a lot, in Inkscape, and you see the intersection and its vertices in magenta. There is no spike.  
  
The output of the second case is:  
![image](https://user-images.githubusercontent.com/334849/175082923-0850aaf8-cfaa-4957-a3ad-30c6e178dc95.png)  
  
Also zoomed in a lot, and it's not a spike.  
  
This is without removing spikes, but with your environment (integer, open, counter clockwise multipolygons)  
  
Note that I'm using Boost Head (~ Boost 1.79) for this test. The intersection for integers is changed recently, a rounding error is fixed and this might reduce the number of spikes.  
  
Is it possible for you to update Boost?

---

## Comment 2

> Username: barendgehrels  
> Created at: 2022-06-22 16:26:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1022#issuecomment-1163339452  

For the reference: the bug was: #930  
The PR fixing this was: #972

---

## Comment 3

> Username: vschoech  
> Created at: 2022-06-22 16:28:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1022#issuecomment-1163342422  

Thank you for the very helpful feedback and the connection to existing issues. I'll check with the latest Boost version and then return to this issue to confirm whether or not it has been resolved. Please be patient, it will take a while.

---

## Comment 4

> Username: vschoech  
> Created at: 2022-07-08 13:40:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/1022#issuecomment-1179002223  

> Is it possible for you to update Boost?  
  
We updated our code base to boost 1.79.0 and I'm happy to confirm that I cannot reproduce this issue any more.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2022-07-13 08:35:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1022#issuecomment-1182934301  

Thanks for conforming @vschoech !  
Closing the issue.
