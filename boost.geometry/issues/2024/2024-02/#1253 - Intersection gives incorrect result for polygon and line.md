# #1253 - Intersection gives incorrect result for polygon and line [Open]

> Username: jdmarsh  
> Created at: 2024-02-29 18:30:13 UTC  
> Updated at: 2025-02-10 18:10:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/1253  

I have a polygon and line which intersects at the very corner, the intersection result is much larger than it should be. The godbolt repro is here: https://godbolt.org/z/TEEaqvcT1  
  
Polygon(red) and line(green)  
![image](https://github.com/boostorg/geometry/assets/972442/a83f8c87-7ddd-40e8-b08a-4688f48d9f1b)  
  
Intersection result(blue) goes way outside of the red triangle, down to the bottom left  
![image](https://github.com/boostorg/geometry/assets/972442/6f1a9165-e1a6-40d5-982c-1a2b01d7007c)  
  
I don't really care whether the actual result is super accurate or not, I don't need a high level of precision, I just need the result to be somewhat sensible and not creating huge lines outside the boundaries of the polygons I'm using to intersect.

---

## Comment 1

> Username: vissarion  
> Created at: 2024-03-26 09:12:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/1253#issuecomment-2019892107  

I can reproduce the issue. Thanks.   
  
The algorithm incorrectly generates the intersection segments using the computed intersection points and the original endpoints of the input segment. Note that if you compute the intersection points (instead of intersection segments) you will get a good approximation. i.e. `std::vector<Point2D> intersections`

---

## Comment 2

> Username: fgreen  
> Created at: 2025-02-10 18:10:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/1253#issuecomment-2648855727  

I hit a case that I suspect is a duplicate of this issue: https://godbolt.org/z/rjnEv44hq  
This case has a segment that starts on a polygon and extends outward, but intersection reports a segment outside of the  polygon.
