# #930 - Intersection does not behave as expected [Closed]

> Username: MelisWillem  
> Created at: 2021-10-27 13:12:18 UTC  
> Updated at: 2022-02-07 09:58:37 UTC  
> Closed at: 2022-02-07 09:58:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/930  

I created a square of width 10, and a flat triangle of height 1 that crosses the square. When calculating the intersection I get the whole plane above the triangle. I would expect to get small triangle back.  
  
example code:  
  
```  
#include<iostream>  
#include<boost/geometry.hpp>  
  
using namespace boost::geometry;  
  
int main(){  
    using point = model::point<int, 2, cs::cartesian>;  
    using poly = model::polygon<point>;  
  
    poly r = {{{0,0}, {0,10}, {10,10}, {10,0},{0,0}}};  
    poly cross = {{{-10,2},{5,3},{20,2},{-10,2}}};  
  
    std::vector<poly> out;  
    intersection(r, cross, out);  
  
    for(auto x : out)  
    {  
        std::cout << wkt(x) << std::endl;  
    }  
  
}  
```  
compiler explorer link: https://compiler-explorer.com/z/GhfhTGz7q  
  
Output: POLYGON((0 2,0 10,10 10,10 2,0 2))  
I would expect to get POLYGON((0 2, 5 3, 10 2, 0 2))

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-10-27 14:23:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-952982629  

Thanks! It looks like a bug.  
  
![obraz](https://user-images.githubusercontent.com/1226951/139085020-0a966781-451f-4c08-bbee-88995cb9b24b.png)

---

## Comment 2

> Username: MelisWillem  
> Created at: 2021-10-27 16:43:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-953110743  

using compiler explorer I check with older versions, it doesn't seem to happen in 1.76 and earlier. So looks like 1.77 specific.

---

## Comment 3

> Username: MelisWillem  
> Created at: 2022-01-07 20:01:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1007698408  

the never version 1.78.0 still contains this bug.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2022-01-08 18:41:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1008100421  

Did you try already with the define `BOOST_GEOMETRY_NO_ROBUSTNESS`?

---

## Comment 5

> Username: MelisWillem  
> Created at: 2022-01-08 19:28:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1008117032  

@barendgehrels thank you for the advice I did not.  I just tried and unfortunately the result is exactly the same.

---

## Comment 6

> Username: Kissh12  
> Created at: 2022-01-14 15:18:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1013216615  

can I work on this?

---

## Comment 7

> Username: barendgehrels  
> Created at: 2022-01-15 14:09:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1013688620  

> can I work on this?  
  
Yes, you can try. But it won't be easy...  
I'll have a look myself next Wednesday.

---

## Comment 8

> Username: Kissh12  
> Created at: 2022-01-16 13:30:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1013876790  

> > can I work on this?  
>   
> Yes, you can try. But it won't be easy... I'll have a look myself next Wednesday.  
  
can I have some help over this as I am new to this

---

## Comment 9

> Username: barendgehrels  
> Created at: 2022-01-16 14:49:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1013889544  

> > > can I work on this?  
> >   
> >   
> > Yes, you can try. But it won't be easy... I'll have a look myself next Wednesday.  
>   
> can I have some help over this as I am new to this  
  
I would suggest then to start with a simpler issue. This is one of the more complex things in the library.  
  
The best starting point is the documentation for contributors.

---

## Comment 10

> Username: barendgehrels  
> Created at: 2022-01-19 09:42:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1016256724  

> @barendgehrels thank you for the advice I did not. I just tried and unfortunately the result is exactly the same.  
  
Thanks - now I check your Compiler Explorer source (thanks!), it won't help indeed, because you use integer coordinates.

---

## Comment 11

> Username: barendgehrels  
> Created at: 2022-01-19 11:07:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1016342841  

It's because of the integer coordinates, there are integer divisions (no rounding, no conversion to floating point). This causes the intersection point to casted to its lower value.  
  
See this picture:  
  
![image](https://user-images.githubusercontent.com/334849/150117914-1dbb0cc2-0cc8-4996-9567-fed3de3baadc.png)  
  
On the left side both intersection points are collocated. On the right side, that doesn't happen just because the truncation works the other way round there (that line goes from top to bottom - on the left side it goes from bottom to top).  
  
It's probably fixable if we don't keep the current behavior to do only integer calculations on integer coordinates. If we properly round it will be better - but if the triangle is even flatter, it will collapse to nothing.  
  
Even with this info, the intersection points are collocated, it should not give that whole upper polygon as intersection - but that's a different thing, a cluster of two collocated intersection points is harder to solve.

---

## Comment 12

> Username: barendgehrels  
> Created at: 2022-01-19 11:12:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1016348774  

Version with rounding, which is probably correct for this input  
![image](https://user-images.githubusercontent.com/334849/150118837-7d49fa2c-245f-4d09-922c-164fb69900b8.png)  
  
@awulkiew you also worked on this, do you think we should change segment-segment intersection to use floating point calculation and then round (instead of integer division and (automatically) trunc?

---

## Comment 13

> Username: awulkiew  
> Created at: 2022-01-19 11:22:08 UTC  
> Updated at: 2022-01-19 11:24:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1016360270  

@Zilleplus why are you using integer coordinates in this case where clearly the output of the operation cannot be represented properly with them?  
  
@barendgehrels I haven't put much thought into it but it seems that either the operation returns correct output (whatever this means) or we clearly state that integral coordinates are not supported by the segments/linestrings/polygons intersection.  
  
Using integral coordinates for the blue polygon doesn't make sense to me. It would if there were not diagonal segments, only squares, horizontal/vertical segments, steps, etc. Would the algorithm return correct result if the blue polygon was a rectangle or if diagonal segments were made out of steps?

---

## Comment 14

> Username: barendgehrels  
> Created at: 2022-01-19 11:25:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1016363450  

Or, diving further into it, we can still keep integer division but round, using techniques like described here:  
https://stackoverflow.com/questions/2422712/rounding-integer-division-instead-of-truncating  
and then not the accepted solution (it gives weird results for negative numbers) but this one:  
```  
int divRoundClosest(const int n, const int d)  
{  
  return ((n < 0) ^ (d < 0)) ? ((n - d/2)/d) : ((n + d/2)/d);  
}  
```

---

## Comment 15

> Username: barendgehrels  
> Created at: 2022-01-19 11:30:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1016369649  

> @barendgehrels I haven't put much thought into it but it seems that either the operation returns correct output (whatever this means) or we clearly state that integral coordinates are not supported by the segments/linestrings/polygons intersection.  
>   
  
We've always supported integral coordinates so we can't state that. And we don't want to for a generic library. We've also long-time users using integer coordinates (for example Volker).  
In general, if the integers are large it will all work fine. If coordinates are small like this, they more depend on rounding and it's actually feasible to provide this rounding (as in my previous comment, it works). It's actually quite weird, or plainly wrong, to truncate an intersection point of (9.99 9.99) to (9,9). Providing rounding will give better results - even though for integer coordinates it won't always be perfect

---

## Comment 16

> Username: barendgehrels  
> Created at: 2022-01-19 11:31:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1016370368  

_(the closing was a wrong button click)_

---

## Comment 17

> Username: MelisWillem  
> Created at: 2022-01-19 14:22:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1016513776  

@barendgehrels your solution is great for my use case, your effort is very much appreciated from my side.  
  
@awulkiew I am working on an applicated that does some kind of integration over a grid, cutting out some piece of a polygon and calculating the volume inside of it. I'd love discuss it further, but not sure if this is the right place. Advice is always appreciated. So yea maybe I should use floating points, could be.  
Missing a little bit of points due to rounding is not such a big deal, but sometimes you get this kind of behavior that really messes things up for me.

---

## Comment 18

> Username: barendgehrels  
> Created at: 2022-01-26 12:51:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1022169059  

> but this one:  
>   
> ```  
> int divRoundClosest(const int n, const int d)  
  
> ```  
  
Well, this one was wrong as well for negative numbers. The one stating that it was correct didn't behave well for 0 nominators. So it's an adaption now.  
  
It's now in review, PR #972

---

## Comment 19

> Username: barendgehrels  
> Created at: 2022-02-07 09:58:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/930#issuecomment-1031277455  

It's merged, closing this issue.  
Thanks for this report @Zilleplus !
