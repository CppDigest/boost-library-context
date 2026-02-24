# #566 - Intersection fails with simple rectangles (1.69) [Closed]

> Username: heriberto-delgado  
> Created at: 2019-03-11 20:47:16 UTC  
> Updated at: 2024-11-16 10:19:42 UTC  
> Closed at: 2024-11-16 10:19:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/566  

(Moving it from the mailing list, to improve its visibility).  
  
The polygons in question are:  
  
```  
POLYGON((16.8739 0.353458,0 0,0.266003 -12.6988,17.1399 -12.3454,16.8739 0.353458))', 0)  
POLYGON((0 0,0.148086 -7.06952,10.1459 -6.86009,9.99781 0.209424,0 0))  
```  
  
Both can be seen here:  
  
![polygons](https://user-images.githubusercontent.com/47340659/54155668-420caf00-440a-11e9-9abd-5c20e1341605.JPG)  
  
When running the following program in Visual Studio 2017, using boost 1.69:  
  
```  
#include <boost/geometry.hpp>  
#include <vector>  
#include <iostream>  
  
int main(int argc, char** argv)  
{  
	boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double>> first;  
	boost::geometry::read_wkt("POLYGON((16.8739 0.353458,0 0,0.266003 -12.6988,17.1399 -12.3454,16.8739 0.353458))", first);  
	boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double>> second;  
	boost::geometry::read_wkt("POLYGON((0 0,9.99781 0.209424,10.1459 -6.86009,0.148086 -7.06952,0 0))", second);  
	std::vector<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double>>> results;  
	boost::geometry::intersection(first, second, results);  
	for (auto& result : results)  
	{  
		std::cout << boost::geometry::wkt(result) << std::endl;  
		std::cout << boost::geometry::is_valid(result) << std::endl;  
	}  
	return 0;  
}  
```  
  
The program returns the following:  
  
```  
POLYGON((0 -8.88178e-16,0.148086 -7.06952,0.148086 -7.06952,0 -8.88178e-16))  
1  
```  
  
, results which do not make any sense at all.   
  
Any ideas?

---

## Comment 1

> Username: barendgehrels  
> Created at: 2019-03-13 18:10:56 UTC  
> Updated at: 2019-03-13 18:40:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/566#issuecomment-472542314  

With the preparations of removing the rescaling, this bug is already fixed. This is recently merged and will be available in Boost 1.70  
  
It runs fine both with and without BOOST_GEOMETRY_NO_ROBUSTNESS defined (with my tests using clang on Ubuntu).  
  
I added these cases to the suite to avoid future regressions. I added both variants (one from mail, one described here) because in the reworked intersection strategy (not yet merged), it did make a difference. But that is also fine now.  
  
**EDIT**: the regression matrix at https://www.boost.org/development/tests/develop/developer/geometry.html will show if it unfortunately would not yet be solved in Visual Studio versions.

---

## Comment 2

> Username: heriberto-delgado  
> Created at: 2019-03-13 19:17:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/566#issuecomment-472567275  

As soon as I got your reply, I downloaded 1.70.0 beta 1 from https://dl.bintray.com/boostorg/beta/1.70.0.beta1/source/boost_1_70_0_b1.zip, attached it to the project, and ran it again.  
  
Unfortunately, 1.70.0 Beta 1 is still returning the results shown above, for the program posted above.  
  
Could it be that I downloaded the wrong version? Or maybe I should get the latest build somewhere else I didn't know?   
  
(If it helps, this is the actual VS2017 project I'm using to do the test: [TestBoostGeometry.zip](https://github.com/boostorg/geometry/files/2963224/TestBoostGeometry.zip) ).  
  
Incidentally, I also noticed that the polygons posted above and the ones in the program are different: The orientation of the second polygon is *reversed* with respect to the first one. My bad. Sorry about that. (In fact, using the reversed polygon, the intersection function returns an empty list, which is completely understandable).

---

## Comment 3

> Username: barendgehrels  
> Created at: 2019-03-13 19:43:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/566#issuecomment-472576402  

Thanks for trying! My recent fixes should be in 1.70 Beta 1, I thought. So maybe it is not yet fixed for VS2017  
  
Let's first wait on the regression matrix (one or two days), I cannot test it now or the coming days anyway. If it still fails I will come back to it (end of March).  
  
About reversal, no problem (our tests correct input before operation).

---

## Comment 4

> Username: awulkiew  
> Created at: 2019-04-07 19:03:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/566#issuecomment-480619717  

@barendgehrels Is this fixed in 1.70? I moved this issue to 1.71 without reading the discussion.  
  
@heriberto-delgado You could check 1.70 RC1: https://dl.bintray.com/boostorg/release/1.70.0/source/  
This is the code that the most likely will be released as 1.70.

---

## Comment 5

> Username: heriberto-delgado  
> Created at: 2019-04-07 19:37:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/566#issuecomment-480622356  

I did check. The issue persists, unfortunately.

---

## Comment 6

> Username: mloskot  
> Created at: 2021-02-15 08:31:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/566#issuecomment-779050811  

Moving this to milestone 1.76

---

## Comment 7

> Username: KonanM  
> Created at: 2021-03-18 11:37:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/566#issuecomment-801849892  

I just wanted to say that we are also affected by this issue and that it's blocking us from updating boost (geometry) to a version newer than 1.68.

---

## Comment 8

> Username: vissarion  
> Created at: 2022-06-07 14:15:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/566#issuecomment-1148735630  

@heriberto-delgado and @KonanM is this issue still persist in `1.79`?

---

## Comment 9

> Username: heriberto-delgado  
> Created at: 2022-06-07 14:38:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/566#issuecomment-1148765883  

Checked against boost::geometry 1.79, but this time in a Xcode project in MacOS. The output of the program changed: now the programs returns *no results at all*, meaning boost::geometry::intersection did not find the intersection of the two input polygons - which is also not the expected result.  
  
So, the issue no longer persists - it changed, and it is still wrong.

---

## Comment 10

> Username: vissarion  
> Created at: 2022-06-07 14:51:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/566#issuecomment-1148781715  

Thanks! I can verify this behavior in Ubuntu with clang-11, the intersection is empty.

---

## Comment 11

> Username: barendgehrels  
> Created at: 2024-11-16 10:19:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/566#issuecomment-2480505216  

Fixed by removing rescaling
