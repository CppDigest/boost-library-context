# #669 - Difference and cartesian_segments yield wrong result [Open]

> Username: henzim  
> Created at: 2020-02-25 11:02:47 UTC  
> Updated at: 2020-02-25 15:10:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/669  

When computing the difference between two polygons that touch at one point but do not overlap boost::geometry::difference yields the wrong result.  
In my original problem the touching point happens to be the starting point of the input polygon.  
  
The polygons are unfortunatly very large and I only posted the code on WandBox  
https://wandbox.org/permlink/0vqkFS71m0zJ65X4  
Also, the problem seems very elusive, because when I tried to create a minimal example around the (presumed) problematic area, the bg::difference suddenly works. (has to do with the start point?)  
  
The polygons look like this:   
Green = inputPolygon  
Red = subtractPolygon  
![image](https://user-images.githubusercontent.com/31598216/75239988-ec204b00-57c3-11ea-9cab-9aaa129e49f5.png)  
![image](https://user-images.githubusercontent.com/31598216/75240110-17a33580-57c4-11ea-9ad5-beb5fe3fda45.png)  
![image](https://user-images.githubusercontent.com/31598216/75240143-2984d880-57c4-11ea-80a3-d284a13ffe47.png)  
Note that the start point of the inner green ring is the touching point.  
  
The often suggested solution  
  *#define BOOST_GEOMETRY_NO_ROBUSTNESS*  
seems to help in some cases:  
  https://svn.boost.org/trac10/ticket/13553  
  https://github.com/boostorg/geometry/issues/548  
In my original problem defining BOOST_GEOMETRY_NO_ROBUSTNESS does help.   
However, as also reported by others  
  https://svn.boost.org/trac10/ticket/13566  
  https://github.com/boostorg/geometry/issues/630  
other (many more!) cases now fail.  
  
**cartesian_segments**  
In #653 I read about setting a higher precision (?) using "cartesian_segments".   
This works much better than defining BOOST_GEOMETRY_NO_ROBUSTNESS.   
However, in some cases it also fails.  
  
I would like to better understand what this does  
- Does the type "long long" define the integer type/precision used when rescaling?   
- Assuming it would always work, which is not the case (cf. case 4 below), would it have any disadvantages?   
- I noticed that the size of the DLL/so grew about 20%, which is OK, will the execution be slower?  
  
**Here is what I tried (c.f. points 1-4 in WandBox-Code)**  
  
1. The original problem with "default_strategy()"   
   This is wrong, the interior ring of the input (first) polygon simply disappears (instead of remaining intact)  
  
2. The original problem with "cartesian_segments"   
   This works  
  
** Minimal example **  
I cut out a piece of the inputPolygon around the subtractPolygon to reduce the input.  
![image](https://user-images.githubusercontent.com/31598216/75240678-09094e00-57c5-11ea-8c28-7beebb2e3482.png)  
Note that the green start point now lies in on the top.  
  
Now the roles have changed...  
  
3. The minimal example with "default_strategy()"  
   This is right  
  
4. The minimal example with "cartesian_segments"   
   This is now wrong, a non-related corner of the input is chipped away (Blue=result)  
![image](https://user-images.githubusercontent.com/31598216/75240856-55548e00-57c5-11ea-8fe0-60a4adbe56fb.png)  
![image](https://user-images.githubusercontent.com/31598216/75240894-63a2aa00-57c5-11ea-8c0e-6df83637cd62.png)  
  
The problem always seems to occur around the start point, could this be the problem?  
  
I have reproduced the problem using  
Windows 10, VS2015, Boost 1.66 on my PC  
Linux, Gcc 8.1, Boost 1.66-1.72 on WandBox  
  
Many thanks in advance!  
  
**EDIT**  
Shifting the start point (std::rotate)  
- solves the problem in 1.  
- does not solve the problem in 4.
