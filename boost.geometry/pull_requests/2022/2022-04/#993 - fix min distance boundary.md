# #993 [buffer] fix min distance boundary [Closed]

> Username: barendgehrels  
> Created at: 2022-04-13 12:40:36 UTC  
> Updated at: 2024-12-14 11:14:36 UTC  
> Closed at: 2022-06-22 11:47:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/993  

This fixes issue #988  
  
There is one regression, an invalid case:  
  
![image](https://user-images.githubusercontent.com/334849/163181367-758a835f-6853-419e-b540-e9676fcbd3cb.png)  
  
I explain it in more detail here.  
  
Turns 5 and 6 (inside the yellow circle, denoted as `5 1` and `6 1`) are tricky.  
  
The calculated intersection point is **right of** the line from which the intersection is calculated. This can happen occasionally. Therefore both points are inside the "other piece" (it is about pieces 3 and 12 marked by the green circle, and these pieces are identical because the linestring is invalid and has a duplicate part).  
I verified it using the robust-side of @tinko92  and indeed, it gives "right of" in both cases. Later it's calculated by the distance-measure and that one gives a measure of `8.88178e-16`.  
  
For this reason I had that threshold of `1.0e-4` (now gone, in this PR, for non-rescaling). But that value is way too much. A value of for example `1.0e-15` works for all cases. But I like to get rid of that threshold at all.  
  
In more general terms: we calculate an intersection P on two lines A and B. Due to FP-precision, P can be located left, on top or right of A and left, on top or right of B. Ideally P is on top of both A and B, of course. But it can be a little-bit off. If we later need the side-information, we get left (1), right (-1) or on-segment (0). Also there is a small gray area for which we have different approaches. Ideally, the two strategies intersection and side should correspond and cooperate, such that if the intersection is calculated, and the side is verified later, it is always 0 for both of the lines. As far as I know we never researched it. Probably a nice challenge (@tinko92 ?)  
  
Because of this reason, this problem will happen more frequently for invalid cases (with duplicate pieces and intersection points).  
  
There is another solution as well: if the intersection points of intersections piece 0/3 and 0/12 are both inside the other (12 and 3), we can detect that and set both of them as "traversable". That would require keeping additional information and using this for detection.  
  
Solutions:  
* accept this regression, it's an invalid case anyway  
* use a threshold but smaller  
* revise intersection/side such that they cooperate (in terms of thresholds) and this never occurs (I don't know if that is possible)  
* detect mutual turn-in-piece occurances with more administration  
* revise buffer  
  
(Revise buffer would be huge: an alternative would be to generate buffers for smaller geometry-parts and union their results - I tried it long ago but maybe things have been changed - and maybe it might be convenient for the geographic buffer as well - the final code would probably be simpler)

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2022-04-13 12:50:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/993#issuecomment-1098012065  

The reported case, a linestring buffer  
  
**With fix:**  
  
![image](https://user-images.githubusercontent.com/334849/163183280-a200ec82-6f46-4738-aa39-99b268d39bcc.png)  
  
**Without fix:**  
  
![image](https://user-images.githubusercontent.com/334849/163183475-852bf88e-0702-42a2-9486-a94f83c268a7.png)  
  
You see that the case without the fix has many green (traversable) turns because the scale of the reported linstring was quite small and therefore the threshold of `1.0e-4` was unfortunate.

---

## Comment 2

> Username: tinko92  
> Created_at: 2022-04-14 20:10:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/993#issuecomment-1099581644  

> In more general terms: we calculate an intersection P on two lines A and B. Due to FP-precision, P can be located left, on top or right of A and left, on top or right of B. Ideally P is on top of both A and B, of course. But it can be a little-bit off. If we later need the side-information, we get left (1), right (-1) or on-segment (0). Also there is a small gray area for which we have different approaches. Ideally, the two strategies intersection and side should correspond and cooperate, such that if the intersection is calculated, and the side is verified later, it is always 0 for both of the lines. As far as I know we never researched it. Probably a nice challenge (@tinko92 ?)  
  
Thanks for the mention, @barendgehrels .  
  
This might be a little tricky but I think there are two practical solutions for what you ask. I am no expert, though, so there might be something better. The following ignores floating-point underflow/overflow which are probably very uncommon:  
  
* Solution 1: The side strategy https://gist.github.com/tinko92/5f242339ae312f66206f2be69184be72 with an intersection strategy that computes P's coordinates up to the machine ε using arbitrary precision arithmetic.  
    * Pro: Most precise for a given coordinate type  
    * Con: every intersection computation will be expensive (I think at least an order of magnitude slower than normally, possibly dominating the runtime of the algorithm that uses intersections even if they didn't before)  
* Variation: Solution 1 but replace the machine ε with a tunable relative error 𝛿 and use a fast error bound test in the intersection strategy to determine, whether the results of the usual intersection formula satisfy this relative error. If they don't, fall back to arbitrary precision arithmetic. Intuitively I think something like 10^4 * ε could be a good choice for 𝛿.  
    * Pro: probably only slightly slower than the simple intersection strategy for non-degenerate inputs (I would expect something like 10-20% slower intersection computations on average)  
    * Con: Less precision for P, slightly more complicated to implement  
  
Limitations for both suggestions:  
* The side strategy is not exact so there are still other inconsistencies possible, like saying some a,b,c and a,b,d are collinear but a,c,d aren't.  
* If there are 2nd order constructions, e.g. intersection point P is then used to calculate another point P2 that should also be collinear with A, the relative error will increase and then it might be inconsistent again, e.g. P2 could be found not collinear with A.  
  
Both solutions are easy to implement if an added dependency on some arbitrary-precision library is acceptable. Solution 2 is slightly more involved because a good error bound test needs to be derived. If no added dependency is acceptable, the implementation would be more complicated.  
  
For completeness and in case I made a mistake, here are the options that imho don't work with reasoning: https://gist.github.com/tinko92/d6c5f966bcdbe8f2b7f4ea4e0331ecf0

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2022-04-15 06:28:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/993#issuecomment-1099890027  

> Both solutions are easy to implement if an added dependency on some arbitrary-precision library is acceptable. Solution 2 is slightly more involved because a good error bound test needs to be derived. If no added dependency is acceptable, the implementation would be more complicated.  
>   
  
Super, thanks a lot @tinko92 !! I will look at it in more detail and try your suggestions next Tuesday.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2022-04-19 09:45:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/993#issuecomment-1102404789  

>     * Solution 1: The side strategy https://gist.github.com/tinko92/5f242339ae312f66206f2be69184be72 with an intersection strategy that computes P's coordinates up to the machine ε using arbitrary precision arithmetic.  
>         
  
  
For the reference I add a link to where you explained the side strategy related to this before: #952

---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2022-05-24 14:44:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/993#pullrequestreview-983370028  

📁 include/boost/geometry/algorithms/detail/buffer/turn_in_piece_visitor.hpp

```diff
 184 |             if (state.m_min_distance > 1.0e-4 || ! state.m_close_to_offset)
 185 |+ #else
 186 |+             constexpr double zero = 0;
```

> Username: awulkiew  
> Created_at: 2022-05-24 14:44:35 UTC  
> Updated_at: 2022-05-24 14:44:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/993#discussion_r880608036  

Shouldn't this be of the same type as `state.m_min_distance`?

> Username: barendgehrels  
> Created_at: 2022-05-29 19:29:20 UTC  
> Updated_at: 2022-05-29 19:29:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/993#discussion_r884314568  

Indeed. I'm considering an alternative fix (that takes a bit longer), but otherwise I'll change it.


---

## Comment 6

> Username: vissarion  
> Created_at: 2022-05-30 10:08:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/993#issuecomment-1140963656  

@barendgehrels do you need a review of the code in this PR? I understand that you are about to do some changes (as proposed by Tinko) so in that case it is better to do a review afterwards. Sorry if I misunderstood and this PR is waiting for me...

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2022-05-30 11:28:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/993#issuecomment-1141041715  

> @barendgehrels do you need a review of the code in this PR? I understand that you are about to do some changes (as proposed by Tinko) so in that case it is better to do a review afterwards. Sorry if I misunderstood and this PR is waiting for me...  
  
Indeed, it's fine to wait. No problem.

---
