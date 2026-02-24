# #161 - Newton-Raphson and Halley iteration: Poor error reporting vs bisection [Closed]

> Username: NAThompson  
> Created at: 2018-11-15 04:49:19 UTC  
> Updated at: 2019-09-21 09:43:50 UTC  
> Closed at: 2019-09-21 09:43:50 UTC  
> Url: https://github.com/boostorg/math/issues/161  

This is perhaps more of a design decision that a bug, but of course the close button is easy to press so I'll report it anyway:  
  
When I give the `boost::math::tools::bisect` functional a function which is strictly positive everywhere (say, x^2 + 1), I get a very nice error message:  
  
```  
Error in function boost::math::tools::bisect<double>: No change of sign in boost::math::tools::bisect, either there is no root to find, or there are multiple roots in the interval (f(min) = 10001).  
```  
  
However, when I pass the same function into `boost::math::tools::newton_raphson` or `boost::math::tools::halley_iterate`, I don't get any error message at all. Instead, I get a root `r` returned, and the condition `|f(r)| < eps` doesn't hold at all.  
  
My proposal is that before returning, `newton_raphson` and `halley_iterate` should check something like `abs(f(r)) < thresh`, and if it doesn't hold, then return `std::numeric_limits<Real>::quiet_NaN()`.

---

## Comment 1

> Username: pabristow  
> Created at: 2018-11-19 09:18:37 UTC  
> Url: https://github.com/boostorg/math/issues/161#issuecomment-439823357  

I've never been quite happy with rootfinding's "what happens when things go wrong".  
  
I think that one is supposed to check the iteration count having reached the set max_count (and waiting a long time if you didn't choose wisely or, worse, kept the default!)  
  
But what you suggest just destroys any evidence and just leaves only a "something went wrong" message :-(  So I hope we can do better with a nice message too.

---

## Comment 2

> Username: NAThompson  
> Created at: 2018-11-19 20:29:15 UTC  
> Url: https://github.com/boostorg/math/issues/161#issuecomment-440031141  

Right, error reporting is a hard problem. It looks like the initial version of this code was written in 2006, pre-`std::optional`. With the benefit of hindsight, I think returning an optional would be the 2018-idiomatic way to communicate the failure. However, that's not a huge issue since we have nans which accomplish roughly the same thing, with the one problem that they don't force the user to do error checking.  
  
The problem good error reporting with Newton's method is that there are so many different failure modes:  
  
- Cycles (say as you see when you try to find the root of f(x) = x^1/3)  
- Nonexistence of a root (such as x^2 + 1)  
- Fractal basin of convergence/divergence to infinity  
  
I'm not sure it is feasible to communicate all these varied failure modes to the user without drastically increasing overhead. (Cycle detection seems hardest to me.) In addition, a good error message requires a throw, and for a function like Newton's where failure is expected often, throw's are generally frowned upon. So I think a nan is the best that can be done.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-11-20 09:06:50 UTC  
> Url: https://github.com/boostorg/math/issues/161#issuecomment-440196766  

Actually most of the things you list are handled as errors:  
* Cycles  
* zero derivative (shooting off to infinity)  
* Infinite higher derivatives (micro-stepping towards the root but never getting there).  
What's missing is:  
* Entering a local minima.  
* Root not bounded by endpoints.  
  
I do notice that the function is noexcept when possible, so adding a throw would be a breaking change, so as you say I guess we're stuck with NaN :(   I'll see what can be done.

---

## Comment 4

> Username: NAThompson  
> Created at: 2019-01-28 20:39:56 UTC  
> Updated at: 2019-01-28 20:40:15 UTC  
> Url: https://github.com/boostorg/math/issues/161#issuecomment-458292597  

I've found a nice reference for solving this problem: Higham's Accuracy and Stability of Numerical Algorithms, chapter 25 gives the limiting residual in terms of a condition number for Newton's method.  
  
So maybe the termination condition should be something like `if (|f(x)| <= cond(newton)*eps) { return x} else { return nan;} `    
  
I should note it's *not* an easy chapter.
