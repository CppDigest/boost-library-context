# #1250 - `boost::geometry::buffer()` of linestring returns inner polygon [Closed]

> Username: Raclamusi  
> Created at: 2024-02-27 17:15:44 UTC  
> Updated at: 2024-03-23 10:41:04 UTC  
> Closed at: 2024-03-23 10:41:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/1250  

I tried to compute the buffer of `LINESTRING(3 2, 2 2, 2 4, 4 4, 4 2, 3 1)` with a distance of 0.5, but did not get the expected result and instead got the inner polygon.  
This problem does not occur with the reverse order linestring, `LINESTRING(3 1, 4 2, 4 4, 2 4, 2 2, 3 2)`.  
  
https://godbolt.org/z/Gfd85v5bG  
  
**Expected Result**: `MULTIPOLYGON(((2.79289 1.5,2 1.5,1.5 1.5,1.5 2,1.5 4,1.5 4.5,2 4.5,4 4.5,4.5 4.5,4.5 4,4.5 2,4.5 1.79289,4.35355 1.64645,3.35355 0.646447,2.64645 1.35355,2.79289 1.5),(3 1.70711,3.5 2.20711,3.5 3.5,2.5 3.5,2.5 2.5,3 2.5,3 1.70711)))`  
**Result with boost-1.84.0**: `MULTIPOLYGON(((3 1.70711,3 2.5,2.5 2.5,2.5 3.5,3.5 3.5,3.5 2.20711,3 1.70711)))`  
  
![Screenshot](https://github.com/boostorg/geometry/assets/61970673/6123ac42-b498-4a86-b0ef-cf096fdead0d)

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-02-28 08:10:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/1250#issuecomment-1968439326  

Thanks for the report, I can reproduce it. Not yet fixed by my last changes.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2024-02-28 21:47:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/1250#issuecomment-1969975607  

It is fixed now. I jumped on it because the case is, geometrically, so simple, it should really not fail.  
But apparently it was still a rare case, needing a condition which should always have been there.  
Great you found it and reported it.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2024-03-23 10:41:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/1250#issuecomment-2016445038  

The fix is merged, closing.
