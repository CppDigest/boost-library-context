# #220 [test][strategies][douglas peucker] make the unit test for this strategy [Merged]

> Username: mkaravel  
> Created at: 2015-02-11 17:33:01 UTC  
> Updated at: 2015-02-19 08:48:42 UTC  
> Merged at: 2015-02-11 18:19:14 UTC  
> Closed at: 2015-02-11 18:19:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/220  

platform portable and platform independent

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-02-11 18:55:45 UTC  
> Updated_at: 2015-02-11 18:56:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/220#discussion_r24521885  

I think we should slightly change one of the offending coordinates instead of this fix. I mean: in the input coordinates. If the distance is so critical here, the test can have another distance. I will check this feature

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-02-11 19:25:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/220#discussion_r24524687  

I don't understand this case. In SQL Server this linestring is not simplified until the distance is about 13.6  
If so, the output is LINESTRING (12 -3, -6 -13, -9 4, 0 -15, -12 5) so it is 4 8 which is removed here. So how can we have for our case both points removed, in one case, and not in another case?  
Don't know the exact algorithm, but it is at least suspicious.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-02-12 09:52:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/220#discussion_r24569410  

The algorithm that has been implemented is the one described in the following wikipedia page:  
http://en.wikipedia.org/wiki/Ramer%E2%80%93Douglas%E2%80%93Peucker_algorithm  
  
What happens is the following. The end points `(12 -3)` and `(-12 5)` are fixed. The remaining points are considered, in order to find the one at maximum distance from the segment of the two endpoints. The points at maximum distance from this segment are `(-6 -13)` and `(0 -15)`; which one is really further away depends on the accuracy of the computations, so rounding errors play a role here (which is why in the unit test there is this check of comparable distances).  
  
Assume for now that the point that is further away from the segment is `(0 -15)`. This point is marked as point to be kept as its distance from the segment is about 15.17 which is greater than the distance passed to the algorithm which is 10. The algorithm then continues recursively with the two linestrings `LINESTRING(12 -3,4 8,-6 -13,-9 4,0 -15)` and `LINESTRING(0 -15,-12 5)`. the second is not simplified as it has only two points. For the first one the segment `(12 -3,0 -15)` is considered and the point that is further away from the segment is `(-9 4)` at distance 19.8. Again this distance is above 10 so the point is kept and the algorithm applied recursively to the two linestrings `LINESTRING(12 -3,4 8,-6 -13,-9 4)` and `LINESTRING(-9 4,0 -15)`. The second has only two points, so it is not simplified. For the first, the distances of `(4 8)` and `(-6 -13)` from the segment `(12 -3,-9 4)` are computed, and `(-6 -13)` is found to be further away, at distance 15.17. So the point `(-6 -13)` is kept and the algorithm continues recursively with the linestrings `LINESTRING(12 -3,4 8,-6 -13)` and `LINESTRING(-6 -13,-9 4)`. The second is not simplified, while for the first the distance of `(4 8)` from the segment `(12 -3,-6 -13)` is computed. This distance is 13.5, which is again above 10, so the point `(4 8)` is kept. The last recursive calls are on the linestrings `LINESTRING(12 -3,4 8)` and `LINESTRING(4 8,-6 -13)`, which both have two points so they are not simplified.  
Bottom line: all points are kept and the input linestring is not simplified at all.  
  
Assume now that the point that is further away from the segment is `(-6 -13)`. This point is kept, and the algorithm recursively considers the linestrings `LINESTRING(12 -3,4 8,-6 -13)` and `LINESTRING(-6 -13,-9 4,0 -15,-12 5)`. For the first linestring there is no simplification (see explanation above in the previous case), while for the second the point that is further away from the segment `(-6 -13,-12 5)` is the point `(0 -15)` at distance 6.32. This distance is below 10, so the linestring `LINESTRING(-6 -13,-9 4,0 -15,-12 5)` is simplified to `LINESTRING(-6 -13,-12 5)`. So the end result is `LINESTRING(12, -3,4, 8,-6 -13,-12 5)`.  
  
Finally, regarding PostGIS: without looking at the sources, it implements exactly the same algorithm, and it behaves in exactly the same way as our implementation does when the coordinates are doubles. At the first recursive step the point `(0 -15)` is found as further away from the segment connecting the points of the original linestring, at distance 15.17. Then essentially the linestring `LINESTRING(12 -3,4 8,-6 -13,-9 4,0 -15)` is considered, and the point further away from it is `(-9 4)` at distance 19.79. Then essentially the next linestring considered is `LINESTRING(12 -3,4 8,-6 -13,-9 4)` and the point further away from it is `(-6 -13)` at distance 15.17. The next linestring considered is  `LINESTRING(12 -3,4 8,-6 -13)` and the distance of `(4 8)` from the endpoints of this linestring is computed. This distance is 13.50087. For an input distance of 10 (like in the unit test), the point `(4 8)` is kept, and the entire original linestring is returned as is. For an input simplifcation distance of more than 13.50087 (like the distance 13.6 in your case), and of course less than 15.17 (because then we would have had simplifications at an earlier stage) the point `(4 8)` is simplified and the final outcome is `LINESTRING(12 -3,-6 -13,-9 4,0 -15,-12 5)`, which is precisely what PostGIS does.  
  
As far as I am concerned, this is a perfectly valid, and IMHO very interesting, example, I do not see any problems whatsoever, and our results are consistent with what PostGIS does.  
  
One final note: all the distances I mentioned above are not really computed by our algorithm, but rather their comparable counterparts, and each comparable distance computed is checked against the square of the simplification distance.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-02-12 18:21:57 UTC  
> Updated_at: 2015-02-12 18:22:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/220#discussion_r24603710  

With "I don't know the algorithm", I meant that I don't know which algorithm is used in SQL Server. Which was indeed not clear from my comment.  
  
The Douglas Peucker algorithm I know very well, I implemented all this code, first together with a collegue back in 1995, and in 2007 again for Boost.Geometry...  
  
For the rest, thanks for your explanations, did not yet read them all but will take some time for that tomorrow.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-02-12 19:06:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/220#discussion_r24609519  

@barendgehrels From the PostGIS online manual:  
http://www.postgis.org/docs/ST_Simplify.html

---
