# #332 Fix special case of intersects(Segment, Box) [Merged]

> Username: awulkiew  
> Created at: 2015-10-21 00:38:51 UTC  
> Updated at: 2017-03-20 14:30:11 UTC  
> Merged at: 2016-01-20 15:53:05 UTC  
> Closed at: 2016-01-20 15:53:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/332  

In a special case when the difference between points' coordinates of a Segment is 0 and the Segment is disjoint the functio returns wrong result. E.g. for:  
  
```  
SEGMENT(2 1 0,2 1 10)  
BOX(0 0 0,10 0 10)  
```  
  
See also:  
http://stackoverflow.com/questions/32457920/boost-rtree-of-box-gives-wrong-intersection-with-segment

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-11-05 13:18:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/332#issuecomment-154057156  

I have not really looked into the details, but I am okay with merging this.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-11-05 14:03:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/332#issuecomment-154065656  

I'd appreciate if you double checked me with this.  
  
The t parameters present in the original algorithm are stored as numerator and denominator pairs (rational). If a t parameter is infinite the denominator equals 0 which is checked above the code I altered.  
  
As for the special case. If a segment is parallel to the box's face and box's edge perpendicular to this face has length = 0 (in other words it's a flat box and segment parallel to it) then t parameters will be equal to positive or negative infinity depending on the side on which the segment lies WRT box. The conditions added in this PR checks if both infinity values has the same sign and returns if they have.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-11-05 14:37:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/332#issuecomment-154078137  

@awulkiew Okay. Will take a closer look and get back to it.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2016-01-19 21:18:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/332#issuecomment-172989821  

@barendgehrels are you ok with merging it?

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2016-01-20 08:56:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/332#issuecomment-173133566  

Yes, I am OK

---
