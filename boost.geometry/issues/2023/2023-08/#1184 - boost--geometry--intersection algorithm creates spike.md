# #1184 - boost::geometry::intersection algorithm creates spike [Closed]

> Username: vschoech  
> Created at: 2023-08-15 15:36:45 UTC  
> Updated at: 2025-03-26 15:04:08 UTC  
> Closed at: 2023-09-13 18:08:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/1184  

My `tc::geo::polygon<int>` type is actually a **multi-polygon**, using a polygon type that is based on **int**, oriented **counter-clockwise** and **open** (not closed). I am using **boost 1.79.0**.  
  
Please consider the following example as it occurred in our code:   
  
```C++  
tc::geo::polygon<int> polygonA;  
boost::geometry::read_wkt("MULTIPOLYGON(((1169 177,2004 177,2004 1977,1262 1977,1169 177)))", polygonA);  
tc::geo::polygon<int> polygonB;  
boost::geometry::read_wkt("MULTIPOLYGON(((1179 371,1175 287,1179 287,1179 371)))", polygonB);  
tc::geo::polygon<int> polygonC;  
boost::geometry::intersection(polygonA, polygonB, polygonC);  
tc::geo::polygon<int> polygonD = polygonC;  
boost::geometry::remove_spikes(polygonD);  
// polygonC=polygon(MULTIPOLYGON(((1179 370,1179 365,1175 287,1179 287,1179 370))))  
// polygonD=polygon(MULTIPOLYGON(((1179 365,1175 287,1179 287,1179 365))))  
```  
  
It was my expectation that when the input does not contain spikes, neither does the output from boost::geometry::intersection. Please clarify whether my assumption is wrong or there is a bug in boost::geometry::intersection.  
  
See also:  
- https://github.com/boostorg/geometry/issues/603  
- https://github.com/boostorg/geometry/issues/1022

---

## Comment 1

> Username: barendgehrels  
> Created at: 2023-08-16 10:01:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/1184#issuecomment-1680318052  

Hi Volker!  
  
Thanks for the report and the clear reproduction scenario. I looked in the issue. I can reproduce it. It seems that the cause is that `open` is not taken into account correctly, somewhere in the intersection procedure.  
  
My extra code:  
```  
    std::cout << std::boolalpha;  
    std::cout << "a: " << boost::geometry::is_valid(polygonA) << " " << boost::geometry::area(polygonA) << " " << boost::geometry::wkt(polygonA) << std::endl;  
    std::cout << "b: " << boost::geometry::is_valid(polygonB) << " " << boost::geometry::area(polygonB) << " " << boost::geometry::wkt(polygonB) << std::endl;  
    std::cout << "c: " << boost::geometry::is_valid(polygonC) << " " << boost::geometry::area(polygonC) << " " << boost::geometry::wkt(polygonC) << std::endl;  
    std::cout << "d: " << boost::geometry::is_valid(polygonD) << " " << boost::geometry::area(polygonD) << " " << boost::geometry::wkt(polygonD) << std::endl;  
```  
  
Delivering (coordinates you also listed):  
```  
a: true 1.4193e+06 MULTIPOLYGON(((1169 177,2004 177,2004 1977,1262 1977,1169 177)))  
b: true 168 MULTIPOLYGON(((1179 371,1175 287,1179 287,1179 371)))  
c: false 156 MULTIPOLYGON(((1179 370,1179 365,1175 287,1179 287,1179 370)))  
d: true 156 MULTIPOLYGON(((1179 365,1175 287,1179 287,1179 365)))  
```  
  
If I make it `closed`, it works as expected:  
```  
a: true 1.4193e+06 MULTIPOLYGON(((1169 177,2004 177,2004 1977,1262 1977,1169 177)))  
b: true 168 MULTIPOLYGON(((1179 371,1175 287,1179 287,1179 371)))  
c: true 156 MULTIPOLYGON(((1179 287,1179 365,1175 287,1179 287)))  
d: true 156 MULTIPOLYGON(((1179 287,1179 365,1175 287,1179 287)))  
```  
  
I don't think it is related to the issues you mention (though they are also about spikes, thanks for the links and the heads up there).  
  
To be complete, a figure (zoomed in), where  
* green = A  
* blue = B  
* green/red dotted = C  
![image](https://github.com/boostorg/geometry/assets/334849/2b82ecbe-f100-40da-a044-ec47521acaf5)  
  
And zoomed in more, where extra  
* orange = D  
  
![image](https://github.com/boostorg/geometry/assets/334849/717e8c20-be5f-4cbc-a8f4-85c98e81273b)  
  
and the spike in `polygonC` is indeed visible.  
  
Side note: the output is here not optimal for integer. Using double, the intersection will follow `polygonB`. But cause of intersection segment logic and rounding to integer coordinates, these points get the same value and a spike is caused.  
  
Picture for double:  
![image](https://github.com/boostorg/geometry/assets/334849/6d2c5be8-b70a-47b0-96e3-0e9dda58572a)  
  
  
and debug info:  
```  
a: true 1.4193e+06 MULTIPOLYGON(((1169 177,2004 177,2004 1977,1262 1977,1169 177)))  
b: true 168 MULTIPOLYGON(((1179 371,1175 287,1179 287,1179 371)))  
c: true 167.938 MULTIPOLYGON(((1179 370.548,1178.73 365.235,1175 287,1179 287,1179 370.548)))  
d: true 167.938 MULTIPOLYGON(((1179 370.548,1178.73 365.235,1175 287,1179 287,1179 370.548)))  
```  
where the area matches better as well. But this is not really related to removing the spike.

---

## Comment 2

> Username: vschoech  
> Created at: 2023-08-16 10:04:04 UTC  
> Updated at: 2023-08-16 10:04:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/1184#issuecomment-1680321597  

Thank you for the quick and thorough reply! I've seen it in 1.79, but I assume you reproduced in 1.83?

---

## Comment 3

> Username: barendgehrels  
> Created at: 2023-08-16 10:44:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/1184#issuecomment-1680373693  

Yes, it's reproduced in 1.83

---

## Comment 4

> Username: barendgehrels  
> Created at: 2023-08-16 11:58:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/1184#issuecomment-1680470018  

I've a concept fix, will create a PR later today (if the concept is fine)

---

## Comment 5

> Username: vschoech  
> Created at: 2023-09-14 07:10:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/1184#issuecomment-1718886364  

@barendgehrels Thank you! :)

---

## Comment 6

> Username: vschoech  
> Created at: 2025-03-26 15:04:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/1184#issuecomment-2754751862  

We updated our code base to boost 1.87.0 and I'm happy to confirm that I cannot reproduce this issue any more. ✔️
