# #207 Fix/make side by triangle permutation invariant [Merged]

> Username: mkaravel  
> Created at: 2015-02-03 19:45:06 UTC  
> Updated at: 2015-02-09 18:49:45 UTC  
> Merged at: 2015-02-09 16:07:10 UTC  
> Closed at: 2015-02-09 16:07:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/207  

Make the `side_by_triangle` strategy permutation invariant: the strategy takes three points as arguments, and its result is in theory invariant under cyclic permutations of these three arguments.  
To make the strategy consistent with respect to cyclic permutations of its three arguments, we cyclically permute them so that the first argument is always the lexicographically smallest point.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-02-03 21:54:01 UTC  
> Updated_at: 2015-02-03 22:16:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/207#issuecomment-72743244  

The side_value was (and is) twice the area of a triangle formed by p,p0,p1 (either positive or negative). That is still the case because you always keep the order p,p1,p2 the same, be it in a permutation of the same sequence. So the polygon formed by p,p0,p1 keeps the same orientation. Maybe you can add this in a comment, like you wrote just above.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-02-03 22:19:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/207#issuecomment-72747807  

On further thought - the new implementation does 4 point-equal-comparisons and 2 less-comparisons more than the old, which was just a simple calculation.  
  
Therefore I propose to make this a separate strategy, keeping the old one intact. We can still consider setting it as the default (or not), but I want to have the additional calculations optional, also because for integer it is not necessary at all...

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-02-03 22:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/207#issuecomment-72749781  

Sure, I will add a comment about the invariance.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-02-03 22:44:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/207#issuecomment-72752116  

I would prefer to add a boolean template parameter that handles the option of performing these checks or not, instead of creating a new strategy.  
Using this template parameter, I can also disable automatically the additional tests for fundamental integral types.  
Is that solution okay, or do you really prefer another strategy?  
  
We also need to think about documentation: do we prefer to have two different strategies documented or just one with an additional template parameter?

---

## Comment 5

> Username: awulkiew  
> Created_at: 2015-02-04 00:07:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/207#issuecomment-72764338  

We should prefer the correctness over the performance.  
  
I think there is no need for additional parameters or strategies. We could use the old code for integral robust coordinates and the new one for floating-point (and probably user-defined) coordinates.  
  
In case this solution wasn't 100% consistent with the intersection calculation IMHO we should use exactly the same method for sides and intersection (i.e. cramer's rule in cartesian CS) which could be even slower.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-02-04 00:18:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/207#issuecomment-72765558  

I agree with Adam. I think it simply suffices to keep the same interface (towards the user) and internally optimize for integral coordinates.  
I would treat user-defined types the same way as floating-point types.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2015-02-04 07:35:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/207#issuecomment-72803573  

Of course we should prefer correctness over performance, I therefore agreed with the solution. But it should be optimal, and that is easy to do here. An automatic solution  (specialization) to avoid the comparisons is OK for me

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-02-04 07:40:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/207#issuecomment-72804075  

@barendgehrels Please see the last commit: the optimization is implemented there.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-02-04 12:58:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/207#issuecomment-72850026  

@awulkiew I do not see the problem. Before calling `relate::less` I have already checked for equality, which means that when `relate::less` is called the points are far enough away.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2015-02-04 14:21:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/207#issuecomment-72861460  

@mkaravel Ah yes, indeed. Besides degenerated segments should be filtered out.

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2015-02-04 21:16:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/207#issuecomment-72941155  

I'm OK with this.

---
