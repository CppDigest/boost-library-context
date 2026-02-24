# #23 Implement ziggurat for exponential random dist (issue #12099) [Closed]

> Username: jagerman  
> Created at: 2016-03-25 20:28:44 UTC  
> Updated at: 2016-07-06 17:33:48 UTC  
> Closed at: 2016-07-06 17:33:26 UTC  
> Url: https://github.com/boostorg/random/pull/23  

This implementation closely follows the existing ziggurat implementation  
in boost::random::normal_distribution; it uses a larger table (257  
instead of 129) because, unlike normal, we don't lose a bit to  
determining the sign.  
  
Boost issue #12099 (https://svn.boost.org/trac/boost/ticket/12099)

---

## Comment 1

> Username: swatanabe  
> Created_at: 2016-03-25 20:40:13 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-201487745  

AMDG  
  
On 03/25/2016 02:28 PM, Jason Rhinelander wrote:  
  
> This implementation closely follows the existing ziggurat implementation  
> in boost::random::normal_distribution; it uses a larger table (257  
> instead of 129) because, unlike normal, we don't lose a bit to  
> determining the sign.  
  
Does the larger table improve performance?  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: jagerman  
> Created_at: 2016-03-25 20:52:11 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-201495084  

Well, it will, of course, but the issue is by how much.  It _probably_ won't be noticeable: in the original paper, Marsaglia and Tsang give the theoretical efficiency (i.e. how often we just need one draw) at 98.9% for the 256, 97.98% for the 128.  (For normal, in contrast, 256 is 99.33, 128 is 98.78%).

---

## Comment 3

> Username: jagerman  
> Created_at: 2016-03-25 21:14:36 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-201507744  

So some testing: it does, apparently, make a noticeable difference: with the 256-element table, I can get around about 138 Mdraw/s, with the 256-table I get about 121Mdraws/s (Sandy Bridge CPU, g++-5.3.1, with -O3 -march=native).  Without -march=native, I get about 128Mdraws/s with 256, and 113Mdraws/s with 128.  So about 13-14% faster with the larger table.  
  
(These results makes me think I must be misinterpreting the meaning of the paper's "efficiency" term).

---

## Comment 4

> Username: swatanabe  
> Created_at: 2016-03-25 21:15:44 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-201508323  

AMDG  
  
On 03/25/2016 02:52 PM, Jason Rhinelander wrote:  
  
> Well, it will, of course, but the issue is by how much.  
  
  There's no of course.  The larger memory  
footprint can make it slower, and this  
could be more significant than the speedup  
if the difference is small enough.  
  
>  It _probably_ won't be noticeable: in the original paper, Marsaglia and Tsang give the theoretical efficiency (i.e. how often we just need one draw) at 98.9% for the 256, 97.98% for the 128.  (For normal, in contrast, 256 is 99.33, 128 is 98.78%).  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: swatanabe  
> Created_at: 2016-03-25 21:19:35 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-201510574  

AMDG  
  
On 03/25/2016 03:14 PM, Jason Rhinelander wrote:  
  
> So some testing: it does, apparently, make a noticeable difference: with the 256-element table, I can get around about 138 Mdraw/s, with the 256-table I get about 121Mdraws/s (Sandy Bridge CPU, g++-5.3.1, with -O3 -march=native).  Without -march=native, I get about 128Mdraws/s with 256, and 113Mdraws/s with 128.  So about 13-14% faster with the larger table.  
>   
> (These results makes me think I must be misinterpreting the meaning of the paper's "efficiency" term).  
  
  The "efficiency" probably indicates how  
often the algorithm avoids calling exp/log.  
The actual performance difference depends on  
how expensive that is.  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: jagerman  
> Created_at: 2016-03-25 21:21:43 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-201511712  

Ah yes, of course, that's exactly right.

---

## Comment 7

> Username: jagerman  
> Created_at: 2016-03-25 21:31:42 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-201519686  

Just for comparison, I tried increasing the normal table to 256 elements as well: it gets slightly better on my system (105 Mdraw/s -> 109 Mdraw/s), but that ~3.8% improvement is pretty small relatively to the 13-14% gain for the exponential.

---

## Comment 8

> Username: jagerman  
> Created_at: 2016-03-25 21:44:34 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-201527485  

(Incidentally, for you or anyone else who might want to experiment with these table sizes, you can regenerate the table with the small program I attached to https://svn.boost.org/trac/boost/ticket/12099, running the executable with arguments E 128, N 256, etc.  It outputs the formatted C++ table values for exponential_distribution.hpp or normal_distribution.hpp; it's then also necessary to change the 8 in "generate_int_float_pair<RealType, 8>" to the appropriate value: for 128, exponential needs a 7 here, normal needs 8; for 256, exponential needs 8 and normal needs 9, etc.)

---

## Comment 9

> Username: swatanabe  
> Created_at: 2016-03-25 21:57:31 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-201534859  

AMDG  
  
The tests look acceptable:  
  
$  
bin.v2\libs\random\test\test_exponential.test\msvc-14.0\release\threading-multi\test_exponential.exe  
-r 10 -t 50000000  
  
running exponential(814.724) 50000000 times: 0.85877565849260118  
running exponential(245.321) 50000000 times: 0.076082975391681074  
running exponential(841.358) 50000000 times: 0.85181463856219319  
running exponential(576.368) 50000000 times: 0.94695305329766843  
running exponential(478.916) 50000000 times: 0.90072179788461226  
running exponential(748.637) 50000000 times: 0.30441477367350545  
running exponential(748.552) 50000000 times: 0.95062524846055918  
running exponential(861.253) 50000000 times: 0.97585497583906933  
running exponential(602.836) 50000000 times: 0.8967119731874893  
running exponential(176.767) 50000000 times: 0.66969024772426267  
  
(Note: The default settings of the tests are designed  
to run fast, so that the regression tests don't take  
forever.  When you're changing the implementation  
it's a good idea to run them with higher settings.)  
  
In Christ,  
Steven Watanabe

---

## Comment 10

> Username: swatanabe  
> Created_at: 2016-03-25 22:51:07 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-201564268  

AMDG  
  
On 03/25/2016 03:14 PM, Jason Rhinelander wrote:  
  
> So some testing: it does, apparently, make a noticeable difference: with the 256-element table, I can get around about 138 Mdraw/s, with the 256-table I get about 121Mdraws/s (Sandy Bridge CPU, g++-5.3.1, with -O3 -march=native).  Without -march=native, I get about 128Mdraws/s with 256, and 113Mdraws/s with 128.  So about 13-14% faster with the larger table.  
  
  We can eliminate half the exp calls, by  
observing that  
  
exp(-x) <= y[i+1] + (x - x[i+1]) \* (y[i] - y[i+1])/(x[i] - x[i+1])  
  
(I think I got that right.)  There may also  
be a fast lower bound.  
  
  In theory, we could use the same trick for  
the normal distribution, but we'd need to  
find the inflection point...  
  
In Christ,  
Steven Watanabe

---

## Comment 11

> Username: jagerman  
> Created_at: 2016-03-26 18:14:32 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-201908151  

> exp(-x) <= y[i+1] + (x - x[i+1]) \* (y[i] - y[i+1])/(x[i] - x[i+1])  
  
It's marginally simpler to replace the first y[i+1] and x[i+1] with y[i] and x[i](which is fine, since they are points on the same line), but just to confirm, I worked it out as well and got the same thing.  
  
> In theory, we could use the same trick for the normal distribution  
  
That's actually really simple: the inflection point for a normal is 1 standard deviation, i.e. in our case, at 1.  And we can do the same short-circuiting trick, but for acceptance instead of rejection, for x values below the inflection point.  
  
So, for normal, the logic is basically:  
  
if x[i+1] >= 1 -> don't do the exp call for points above the line (they are rejected)  
if x[i] <= 1 -> don't do the exp call for points below the line (they are accepted)  
  
The block containing the inflection point will have x[i+1] < 1 < x[i], and so won't be caught by either of the above, and still gets a full exp() call.  
  
The commit I just made (712da22) implements this for both exponential and normal; with that change, drawing from both distributions is about 7% faster on average on my system.

---

## Comment 12

> Username: swatanabe  
> Created_at: 2016-03-26 19:07:40 UTC  
> Updated_at: 2016-04-20 16:18:00 UTC  
> Url: https://github.com/boostorg/random/pull/23#discussion_r57517986  

Is there a reason for this change?

---

## Comment 13

> Username: swatanabe  
> Created_at: 2016-03-26 19:14:26 UTC  
> Updated_at: 2016-04-20 16:18:00 UTC  
> Url: https://github.com/boostorg/random/pull/23#discussion_r57518072  

This probably makes no measurable difference, but does the compiler CSE y[i]-y[i+1] and y[i+1]-y[i] on the previous line?

---

## Comment 14

> Username: swatanabe  
> Created_at: 2016-03-26 20:09:38 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-201923059  

AMDG  
  
On 03/26/2016 12:14 PM, Jason Rhinelander wrote:  
  
> The commit I just made (712da22) implements this for both exponential and normal; with that change, drawing from both distributions is about 7% faster on average on my system.  
  
Great.  Now, for a lower bound.  
  
We can use the tangent at x_i, by exploiting the  
fact that d/dx exp(-x) = -exp(-x):  
  
exp(-x) >= y[i] + (x - x[i]) \* -y[i]  
  
or we can compute a magic constant C such that:  
  
exp(-x) + C >= y[i] + (x - x[i]) \* (y[i] - y[i+1])/(x[i] - x[i+1])  
  
m_i = (y_i - y_{i+1})/(x_i - x_{i+1})  
g_i(x) = y_i + m(x - x_i)  
h_i(x) = g_i(x) - e^-x  
C_i = max(h(x))  
C = max(C_i)  
  
h_i(x) has a single local maximum which is in (x_i, x_i+1)  
d/dx h_i(x) = e^-x + m = 0  
e^-x = -m  
x = -ln(-m)  
  
therefore  
C_i = y_i + m(-ln(-m) - x_i) + m  
  
Actually, now that I've worked through both of these  
I'm liking the tangent line approach a lot more.  
  
In Christ,  
Steven Watanabe

---

## Comment 15

> Username: jagerman  
> Created_at: 2016-03-26 20:32:37 UTC  
> Updated_at: 2016-04-20 16:18:00 UTC  
> Url: https://github.com/boostorg/random/pull/23#discussion_r57519102  

I noticed a very slightly performance improvement (1 CPU clock cycle) in performing that calculation this way.  Any of  
  
`exp(-0.5*x*x)`, `exp(x*x/-2)` and the above show the same 1 clock cycle improvement over the original.  That said, it's a very minor optimization, but not one that gcc is doing on its own (even with -O3).

---

## Comment 16

> Username: jagerman  
> Created_at: 2016-03-26 20:54:27 UTC  
> Updated_at: 2016-04-20 16:18:00 UTC  
> Url: https://github.com/boostorg/random/pull/23#discussion_r57519412  

I didn't check, but I think it's slightly more natural to reverse both the RHS subtractions anyway, which also gives us the identical subexpression in 2 (or 3, for normal) places; fixed in f4f71a8.

---

## Comment 17

> Username: jagerman  
> Created_at: 2016-03-29 02:57:11 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-202681624  

I've spent several hours over the past two days to work this out mathematically, and calculated just exactly how many exp calls we would save (on average) either way. The general approach here is to figure out the total region of the end rectangles, where all of this matters, then calculate the proportion of that area eliminated by the different approaches, weighting each rectangle by the probability of ending up in that rectangle.  
  
# Exponential  
  
This is really easy to optimize, as it turns out, because for just about every one of the 255 areas, e^-x is very closely approximated by a straight line. Using your first-mentioned approach (i.e. the tangent-at-x[i] approach), which I'll call T1 (since it's a first-order Taylor series expansion) with a 256-size table, we could avoid 98.61% of exp calls (that's including the 50% we're already avoiding with my previous commit). Switching the Taylor approximation to the left corner (i.e. using a first-order Taylor approximation at x[i+1]) gives a slightly better result of eliminating 98.67% of the exp calls.  
  
For a 128-sized table, those go down (slightly) to 98.12% (x[i+1]) and 98.05% (x[i]). Even dropping the table size all the way down to 8, this will still eliminate 90.30% or 89.59% of exp calls.  
  
I also explored the magic constant approach, choosing from 1-4 constants (these numbers keeping the table size at 256). Using 2 constants (one for regions 1-249, one for 250-255) this comes up to 97.93%; using 3 (one applying to i=1-6 and 235-249; one for i=7-234, and one for 250-255) brings the elimination rate up to 98.68%—i.e. the same as the (simpler) T1-at-x[i+1] approach. It takes 4 different constants to get any better, but even then, not by much (98.88%), and the constant selection rules are basically the same as 3, but with the 4th constant applying only to i=255.  
  
The results for a 128 table size are similar: it takes at least 3 constants to do better (98.30% instead of 98.21%), and a 4th improves things only a little (to 98.57%).  
  
So basically, using magic constants really isn't worthwhile for drawing exponentials.  
  
So the (theoretically) optimal approach here appears to be using the T1 approximation at x[i+1]. (I also tried a table reduction to 128, but that still had noticeably worse performance).  
  
# Normal  
  
I started off with the same approach for the normal draws. The biggest difficulty here is dealing with the i=127 case, which looks like this:  
[Image of normal, i=127](https://imaginary.ca/normal-127.png)  
Any line we choose that lies above the curve is going to miss a lot, and we're _already_ missing a lot of probability space between the diagonal and the curve.  
  
What makes it especially bad is that, when we randomly end up with i=127, we are _always_ in this case, while most others end up there only with a (fairly low) probability. Together, these imply that, about 29% of the time when we need to evaluate exp, we're in this case.  
  
The first thing to note is that a Taylor approximation at the left corner is going to be useless here: the tangent has slope 0, and so will eliminate nothing at all; it turns out that, unlike the exponential case, where the two sides are basically the same, it is much better for normal to always evaluate at the right corner, i.e. a Taylor approximation at x[i] rather than x[i+1]. Doing that everywhere except the inflection region (which is at i=101) eliminates 90.95% of the exp calls. Not bad, but still far away from the gain for exponential. The T1-at-x[i+1], on the other hand, only manages 83.54%; much (but not all) of that difference is from the i=127 case.  
  
The tangent is almost the same as the exponential, but with an extra factor of x[i]:  
  
y[i] + (x[i] - x) \* y[i] \* x[i]  
  
exp(-x^2/2) is greater than this in the convex regions (x > 1) and below it in the concave regions (x < 1).  
  
Magic constants can get the avoidance rate up a little, but again, we'd need at least 3 to do better than the T1 approach (91.53% for 3, 92.41% for 4). So again, the magic constant approach is basically not worthwhile.  
  
However, we can do much, much better (in terms of the probability of eliminating an exp) by using a pair of _2nd_-order Taylor approximations instead of the diagonal+T1 approach. This has the biggest impact in that i=127 case, turning our rejection lines into these instead:  
  
[Image of normal, i=127, T2 bounds](https://imaginary.ca/normal-127-T2.png)  
where now we only have to resort to exp calls only in between the orange and red lines instead of between the diagonal and some tangent above the curve.  
  
T2 approximations of exp(-x^2/2) (actually, I think, Taylor approximations of this of any order) have the useful (and critical) property that, as long as we don't cross the inflection point, the Taylor series approximation at x[i] is always on one side of exp(-x^2/2), and the T2 approximation at x[i+1] is always on the other (whether above or below depends on whether we're in the convex or concave region of the density). The downside is that a T2 approximation is, of course, more costly to compute than the T1 and diagonal approximation of the other approach.  
  
The approximation curves are:  
  
yi + yi_(xi-x)_(xi + 0.5_(xi_xi - 1)*(xi-x))  
  
or more naturally (but slower to compute):  
  
yi + xi_yi_(xi-x) + (xi^2 - 1)_yi_(xi - x)^2 / 2  
  
where yi and xi are y[i] and x[i] for one bound and y[i+1] x[i+1] for the other. (Which of these is the upper or lower bound on exp(-x^2/2) depends on whether we're in the concave or convex side of the inflection point, just like the diagonal+T1 approach).  
  
If we used these T2 bounds in all cases, we'd eliminate 99.07% of exp calls; the inflection point case makes up just about half of the remaining exp calls (0.45%). But, of course, we would now be carrying out T2 calculation all the time.  
  
# Coding it up  
  
So I implemented all of this and started testing and benchmarking the speed of draws under the different approaches, by using a simple program that draws and sums 200 million values.  
  
The results confirmed my (theoretical) calculations above very closely: T2 bounds eliminated 99.064% of the exp calls, and the T1+diagonal approach eliminated 90.941%. T1+diagonal at x[i] instead of x[i+1] eliminated 83.52%.  
  
As for speed, T2, despite being not all _that_ much more complicated than T1+diagonal, consistently loses out to it (tested under the two g++ versions and CPUs I mentioned earlier). I tried various approaches: using T2 just for the i=127 case, using it for the first few i values and last couple i values, using it for all the concave cases (i>=102); but on average, it was always just slightly slower than just using the (simpler) tangent method. It _is_ faster in the i=127 case, but the cost of the increased code complexity (and more branches to decide which bounds to calculate) appears to make the whole thing slower overall.  
  
So what I've commited now (in 698634f) adds the (linear) tangency elimination, as I discussed above, plus some other small optimizations I found along the way: I found that it's consistently faster by a small amount (in both the T1+diagonal or the T2 versions) to compute both bounds, even if we only need to check one of them. (This is probably only true with SSE-like instruction sets, where multiple products or sums can be computed at once, and likely also only true for doubles or floats--but both of those seem like relatively common targets).  
  
I also verified all of this before benchmarking, by generating a stream of 50 million random normals and exponentials, using mt19937 with the same seed, and using the different rejection methods compared with the numbers generated under a simple "y < f(x)" rejection method, to verify that every case gives exactly the same 50 million values.  It does (i.e. none of these are rejecting when y < f(x) wouldn't).

---

## Comment 18

> Username: swatanabe  
> Created_at: 2016-03-29 17:00:30 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-202998583  

AMDG  
  
  Wow.  Thanks for doing this.  I've looked over  
your current version briefly and I only have  
minor nits:  
- Please add a citation for the original paper  
  (Marsaglia and Tsang) in the doxygen comment.  
- The comments in exponential_distribution  
  should explain briefly what y_above_ubound  
  and y_above_lbound mean mathematically.  
  (normal_distribution is okay, as it is.)  
- 'y_above_lbound <= 0':  
  This is correct, but a bit subtle.  
  The only case where '<' vs. '<=' matters for  
  correctness is at the endpoint, which I think  
  is unreachable, because y \in [y_i, y_{i+1}).  
  The half-open range means that y = f(x) = y_{i+1}  
  is impossible.   If it were possible, the  
  correct test would be '<' to be consistent  
  with 'y < f(x)'.  (N.B. This really only applies  
  to normal_distribution, where a change in behavior  
  is undesirable.)  
  
In Christ,  
Steven Watanabe

---

## Comment 19

> Username: swatanabe  
> Created_at: 2016-03-29 17:19:16 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-203008173  

AMDG  
  
On 03/29/2016 11:00 AM, Steven Watanabe wrote:  
  
> because y \in [y_i, y_{i+1}).  
> The half-open range means that y = f(x) = y_{i+1}  
> is impossible.  
  
  That would be true if we were dealing with  
real arithmetic.  Unfortunately, floating point  
numbers are not the same as real numbers.  
I suspect that both bounds may give different  
results from f(x) near the endpoints.  You  
might need to use float to reproduce this,  
since double has enough precision that you  
could run for years without ever seeing it.  
This also depends heavily on the implementation  
of exp.  I don't think that library implementations  
are guaranteed to be accurate to 0.5 ulp.  
Anyway, if this can happen, there's no good way  
to avoid it, so you can just ignore this point.  
  
In Christ,  
Steven Watanabe

---

## Comment 20

> Username: jagerman  
> Created_at: 2016-03-29 18:57:30 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-203050526  

It is such a rare event that changing it to < won't reduce performance, and I can see some (extremely unlikely) cases where floating point rounding might conceivably give us an accepted approximation value when exp does not--particularly in the central regions of both normal and exponential, where the linear approximation is very, very close to the exp.  
  
So since it isn't going to cost anything meaningful (in a probabilistic sense), but is marginally more conservative, I agree that it should be changed.

---

## Comment 21

> Username: jagerman  
> Created_at: 2016-03-29 19:20:07 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-203060871  

> (N.B. This really only applies to normal_distribution, where a change in behavior is undesirable.)  
  
Just a comment on this: there _will_ be a slight change in behaviour in normal_distribution as a result of these changes, because normal_distribution depends on exponential_distribution for draws from the tail, and exponential_distribution now advances the RNG engine.  
  
Comparing a sequence of, say, 10 normal values with 1.60.0, before we would get a sequence such as:  
  
A B C D E F G H I J  
  
If one of those, let's say C, required a tail draw, the value of C changes, and we skip D and E (because the engine advanced twice in the two exponential_distribution calls we use to generate the C replacement).  So now we could get:  
  
A B C' F G H I J L M  
  
where C' != C, but is still in the tail.  It's also possible to use up more than 2 engine values for the normal tail draw: each exponential uses another with probability 1/256 (and a third with prob. 1/256^2, and so on), and then there's also a non-zero probability that we need to repeat the pair of exponential draws in normal's generate_tail.

---

## Comment 22

> Username: jagerman  
> Created_at: 2016-03-29 22:40:45 UTC  
> Updated_at: 2016-03-29 22:41:07 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-203142992  

I worked out that it is safe to treat the inflection region as if it is convex, which slightly simplifies the conditions that need to be checked.  
  
Partly to prove it to myself: since the slope at (x,y)[102]--the top left corner of the inflection point region (i=101)--is less than (i.e. steeper than) the diagonal from (x,y)[101] to (x,y)[102], the diagonal will be an upper bound from (x,y)[102] to the inflection point; since it will be above the inflection point, it will also be above the line segment from the inflection point to the lower corner--and since _that_ line segment is already an upper bound of this convex part of the curve, and thus the diagonal is an upper bound everywhere in the region.  
  
As for the lower bound, the tangent at the lower-right corner--that is, at (x,y)[101]--is below the curve from the lower-right corner to the inflection point, and since it's below the inflection point, it's also below the line segment that runs from the inflection point to the top-left corner, which is itself already a lower bound on the curve; hence the tangent is a lower bound everywhere.  
  
Since both of these are what we rely on in the convex case, the inflection point can thus be safely included in the convex case.  
  
(If we were using the left corner tangent instead of the right, the equivalent necessary and sufficient condition would be that the slope at the _right_ corner is steeper than the diagonal; if satisfied, the inflection region would then be grouped into the concave region instead of the convex region).  
  
As I pointed out in the comment in the commit, however, this is basically just lucky: if normal had used a 256-size table instead, it wouldn't have worked.

---

## Comment 23

> Username: jagerman  
> Created_at: 2016-06-07 22:48:53 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-224437833  

Now that 1.61.0 is released, is there anything else still needed to get this into master (for 1.62.0)?

---

## Comment 24

> Username: swatanabe  
> Created_at: 2016-06-07 22:55:13 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-224439007  

AMDG  
  
On 06/07/2016 04:48 PM, Jason Rhinelander wrote:  
  
> Now that 1.61.0 is released, is there anything else still needed to get this into master (for 1.62.0)?  
  
I just need to find time to review and merge it.  
  
In Christ,  
Steven Watanabe

---

## Comment 25

> Username: swatanabe  
> Created_at: 2016-07-06 17:33:48 UTC  
> Url: https://github.com/boostorg/random/pull/23#issuecomment-230846488  

AMDG  
  
I had to make one small change:  
- MSVC doesn't accept "and" and "or" as keywords.  
  
In Christ,  
Steven Watanabe

---
