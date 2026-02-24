# #537 - simplify() produces empty output [Closed]

> Username: pwiecz  
> Created at: 2018-12-10 16:07:56 UTC  
> Updated at: 2018-12-10 18:53:49 UTC  
> Closed at: 2018-12-10 18:53:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/537  

Hi,  
I've recently upgraded boost from 1.65.1 to 1.68.0 (and VS runtime libraries to v141) my software started producing strange results.  
boost::geometry::simplify for some rings for which before it was producing a non-empty results it started producing empty output.  
  
Following snippet is printing 0:  
```C++  
	typedef boost::geometry::model::d2::point_xy<float> point_type;  
	typedef boost::geometry::model::ring<point_type> ring_type;  
  
	ring_type ring{  
		{98.6063,56.8823},  
		{98.5298,57.1487},  
		{98.385,57.6527},  
		{98.3119,57.6632},  
		{98.2432,57.6703},  
		{98.1997,57.6743},  
		{97.6743,57.7267},  
		{97.6061,57.7301},  
		{97.5121,57.7355},  
		{97.4789,57.7371},  
		{97.2124,57.7525},  
		{97.0313,57.7609},  
		{96.8298,57.7619},  
		{96.7531,57.7629},  
		{96.4764,57.764},  
		{96.3876,57.765},  
		{96.3292,57.7627},  
		{95.9707,57.7482},  
		{95.9558,57.7476},  
		{95.7771,57.6833},  
		{95.3732,57.5153},  
		{95.3447,57.5034},  
		{95.2386,57.4592},  
		{94.7404,57.2227},  
		{94.7133,57.2098},  
		{94.5187,57.1054},  
		{94.1974,56.9327},  
		{94.1525,56.9057},  
		{93.7179,56.6444}  
	};  
  
	ring_type simplifiedRing;  
	boost::geometry::simplify(ring, simplifiedRing, 0.01f);  
  
	printf("%d\n", (int)simplifiedRing.size());  
```  
  
I'm using MSVC 2017 15.9.3.  
  
Best,  
Piotr  
![output](https://user-images.githubusercontent.com/136140/49744849-00028e80-fc9e-11e8-8a08-738bb4c9f21c.png)

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-12-10 16:35:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/537#issuecomment-445880944  

Hi Piotr,  
  
I checked it in VS2015 and can reproduce. The problem is that by default the order of points in a ring is clockwise and that rings are closed (has to have the first and last point of the range equal) but your data is counter-clockwise and open. While order seems to not change the result, the closure does change it. So you should either change the type to match the data, e.g.:  
  
    boost::geometry::model::ring<point_type, false, false>  
  
or change the data to match the type:  
  
    boost::geometry::correct(ring);  
  
After fixing the code (first fix, altering the type, data still CCW and open) I get:  
  
![image](https://user-images.githubusercontent.com/1226951/49746460-8e2c4400-fca1-11e8-8111-7e844125cdde.png)  
  
Adam

---

## Comment 2

> Username: pwiecz  
> Created at: 2018-12-10 16:50:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/537#issuecomment-445886814  

Thanks a lot!  
I've just duplicated the first point at the end and it also seems to fix the problem.  
  
It strange that this "issue" seems to happen so randomly, out of thousands of polygons I'm simplifying it producec empty results just for a few of them.  
I guess, that  just an undefined behaviour to use incorrectly typed rings?

---

## Comment 3

> Username: awulkiew  
> Created at: 2018-12-10 18:42:05 UTC  
> Updated at: 2018-12-10 18:53:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/537#issuecomment-445925125  

Yes, if the geometry is invalid from whatever reason (wrong ordering or closure, self-intersecting rings, etc.) the result is undefined. Some algorithms may work in this case as expected but not all of them or not for all data.
