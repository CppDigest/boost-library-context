# #1166 - Documentation bug for TOMS 748 [Open]

> Username: NemoPublius  
> Created at: 2024-07-30 19:55:17 UTC  
> Updated at: 2024-08-02 17:12:03 UTC  
> Url: https://github.com/boostorg/math/issues/1166  

At least, I am pretty sure this is a documentation bug.  
  
I asked this question on StackOverflow:  
  
https://stackoverflow.com/q/78795676/  
  
The [TOMS 748 documentation](https://github.com/boostorg/math/blob/boost-1.85.0/doc/html/math_toolkit/roots_noderiv/TOMS748.html#L106) states:  
  
> f(x) need not be uniformly increasing or decreasing on x and may have multiple roots. However, the bounds given must bracket a single root.  
  
The responses -- and a straightforward reading of the TOMS 748 paper -- suggests that this is not correct, and that the bounds merely must bracket _at least one_ root.  
  
This may seem like a trivial difference, but for my application it matters a lot. Thanks.

---

## Comment 1

> Username: NAThompson  
> Created at: 2024-07-31 15:46:00 UTC  
> Url: https://github.com/boostorg/math/issues/1166#issuecomment-2260832026  

So the recommended diff is?  
  
> the bounds given must bracket a ~~single~~ root.  
  
That seems ok.  
  
FWIW, we do have a [cubic solver](https://github.com/boostorg/math/blob/f7e6597a6044bd2a2ba2900479c550bee0fda337/doc/roots/cubic_roots.qbk#L4).

---

## Comment 2

> Username: plopresti  
> Created at: 2024-07-31 23:06:11 UTC  
> Url: https://github.com/boostorg/math/issues/1166#issuecomment-2261633803  

@NAThompson I would probably go with "...must bracket at least one root".  
  
Thanks for the reference to the cubic solver. I believe the bracketing approaches are more appropriate for my use case, but speed is also a concern, so I will probably experiment with both.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2024-08-01 18:09:40 UTC  
> Url: https://github.com/boostorg/math/issues/1166#issuecomment-2263667872  

Hmmm, I'm nervous about this, I suspect a more accurate wording would be "must bracket an odd number of roots", because an even number _could_ be zero and would break the algorithms invariants?  If you have a concrete use case (or can concoct some examples), I'll add it to the tests as well just to be sure ;)

---

## Comment 4

> Username: NemoPublius  
> Created at: 2024-08-01 18:16:25 UTC  
> Url: https://github.com/boostorg/math/issues/1166#issuecomment-2263679990  

@jzmaddock I am pretty sure "at least one" is correct (and rules out zero).

---

## Comment 5

> Username: jzmaddock  
> Created at: 2024-08-01 18:33:11 UTC  
> Url: https://github.com/boostorg/math/issues/1166#issuecomment-2263713072  

My point is that any bracketing algorithm can not tell the difference between 2 roots and zero roots, consider bisecting a function with two roots at say x=2 and x=3 with a starting range of [0,10].  
  
Now, f(0) and f(10) have the same sign, we then examine f(5) and hey... that has the same sign as well!  So which way do we jump next?  There is simply no way to tell that the [0,5] interval has those two roots.

---

## Comment 6

> Username: NemoPublius  
> Created at: 2024-08-01 18:39:02 UTC  
> Updated at: 2024-08-01 18:50:49 UTC  
> Url: https://github.com/boostorg/math/issues/1166#issuecomment-2263724041  

@jzmaddock  
  
Your example does not satisfy the preconditions.  
  
>  It is a precondition that a < b and that a and b bracket the root to find so that f(a) * f(b) < 0.  
  
As far as I know, all of these "bracketing" algorithms work similarly, falling back to bisection when things get weird. They all require that f(a) and f(b) have opposite signs and that [a,b] contain at least one root.  
  
TOMS 748 fits this description, anyway.  
  
Note that the number of roots in [a,b] can be even if some have multiplicity. TOMS 748 (and pretty much any "bracketing" algorithm, I think) will still work.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2024-08-02 14:20:47 UTC  
> Url: https://github.com/boostorg/math/issues/1166#issuecomment-2265503688  

>Your example does not satisfy the preconditions.  
  
Well.. that's why I gave it as an example, if we exclude the special cases of roots that touch but do not cross the origin, then requiring `f(a)*f(b) < 0` is the same as saying "an odd number of roots".  It is of course a much more accurate precondition.  
  
Whatever, I'll tidy up the language, and add some tests on multiple roots in oscillating functions just to be sure.

---

## Comment 8

> Username: NemoPublius  
> Created at: 2024-08-02 16:54:41 UTC  
> Url: https://github.com/boostorg/math/issues/1166#issuecomment-2265794211  

> > Your example does not satisfy the preconditions.  
>   
> Well.. that's why I gave it as an example, if we exclude the special cases of roots that touch but do not cross the origin, then requiring `f(a)*f(b) < 0` is the same as saying "an odd number of roots". It is of course a much more accurate precondition.  
  
I am sorry, but I still do not see the problem.  
  
Why would we exclude the case of roots that touch but do not cross the origin? As far as I know, TOMS 748 imposes no such restriction.  
  
And the requirement `f(a)*f(b) < 0` is already stated in the documentation here:  
  
https://github.com/boostorg/math/blob/boost-1.85.0/doc/html/math_toolkit/roots_noderiv/TOMS748.html#L116-L119  
  
So I am pretty sure the documentation is fine as it is, except that "a single root" should read "at least one root".  
  
> Whatever, I'll tidy up the language, and add some tests on multiple roots in oscillating functions just to be sure.  
  
Thank you.  
  
The multiplicity case -- e.g. touching but not crossing the axis -- is actually somewhat interesting, since it is the sort of thing the derivative-based solvers sometimes struggle with. This is one reason I am leaning towards the bracketing solvers.

---

## Comment 9

> Username: NAThompson  
> Created at: 2024-08-02 17:10:00 UTC  
> Updated at: 2024-08-02 17:12:03 UTC  
> Url: https://github.com/boostorg/math/issues/1166#issuecomment-2265816590  

> Why would we exclude the case of roots that touch but do not cross the origin? As far as I know, TOMS 748 imposes no such restriction.  
  
It's not just derivative solvers-in this case the condition number of rootfinding is infinite. In other words, this is a property of the *problem*, not of the method to solve it. See Corless, A Graduate Introduction to Numerical Methods from the Viewpoint of Backward Error Analysis, equation (3.20).  
  
As a practical matter, however, you may be right-when you have infinite ill-conditioning different methods can do better or worse-though it may be quite difficult to know if its just a particular tested case or a fundamentally better interaction of the method with the problem.
