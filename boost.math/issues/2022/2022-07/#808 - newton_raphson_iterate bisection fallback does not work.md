# #808 - newton_raphson_iterate bisection fallback does not work [Open]

> Username: wthrowe  
> Created at: 2022-07-26 20:01:03 UTC  
> Updated at: 2022-07-28 16:10:54 UTC  
> Url: https://github.com/boostorg/math/issues/808  

The documentation for `newton_raphson_iterate` states that "Out-of-bounds steps revert to bisection of the current bounds", which should be robust, but the implementation fails to bisect correctly in some cases.  The following program requests the root of a quartic with the sole root 1 in the bounding interval, but with an initial guess on the wrong side of an extremum that causes a step out of bounds.  The returned result is approximately the endpoint of the interval farthest from the actual root, which should have been eliminated by a single bisection.  
  
```c++  
#include <boost/math/tools/roots.hpp>  
#include <iostream>  
#include <utility>  
  
int main() {  
  const auto quartic = [](const double x) {  
    return std::make_pair((x * x - 1.0) * (x * x + 0.1),  
                          2.0 * x * ((x * x - 1.0) + (x * x + 0.1)));  
  };  
  
  const double result =  
      boost::math::tools::newton_raphson_iterate(quartic, 0.5, 0.1, 1.1, 5);  
  std::cout << result << "\t" << quartic(result).first << "\n";  
  return result;  
}  
```  
  
Output:  
```  
0.10625 -0.110033  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-07-27 10:13:07 UTC  
> Url: https://github.com/boostorg/math/issues/808#issuecomment-1196536601  

Unfortunately, I don't think this is solvable in all cases.  
  
In this situation, the initial NR step goes out of bounds to the left, we therefore assume (erroneously in this case) that there is a root in the interval `[0.1, 0.5]` and bisect that interval.  Subsequent NR steps succeed, but only lead us to the left endpoint 0.1.  
  
  
Now, we could have bisected the whole interval `[0.1, 1.1]` and in this case this would have led to the correct root, but consider a curve that looks more like:  
  
![nr](https://user-images.githubusercontent.com/5011768/181220822-83f19298-b7af-448d-bfb9-7b5ac78aa693.png)  
  
With P1 as the initial guess.  This time we go out of bounds to the right, and bisection of `[p1, b]` leaves us in the correct domain, where as bisecting `[a,b]` would have led to a dead end (and another bug).  
  
Basically, if we are given an initial guess we the right to assume that it is "downhill" from that guess to the root.  For example, one could easily construct curves that look a lot like yours, but which have one or more roots between `[0.1, 0.5]` in addition to the root on `[0.5,1.1]`, it would be a reasonable expectation in that case that we would follow the direction indicated by the derivative at the initial guess.  
  
I realise this sounds like a cop-out, and I really am committed to making this routine as robust as possible, not least because NR routines have a reputation for exploding in all sorts of situations.  But I just don't think it's possible to make them completely fool-proof in all situations.  
  
Suggestions welcome!

---

## Comment 2

> Username: NAThompson  
> Created at: 2022-07-27 16:20:20 UTC  
> Url: https://github.com/boostorg/math/issues/808#issuecomment-1196971956  

@wthrowe : Does the Halley iterate succeed? In general I find the Halley iterate has a more sane basin of convergence and moreover it can degrade gracefully to a newton iterate.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-07-27 16:31:14 UTC  
> Url: https://github.com/boostorg/math/issues/808#issuecomment-1196983680  

>Does the Halley iterate succeed? In general I find the Halley iterate has a more sane basin of convergence and moreover it can degrade gracefully to a newton iterate.  
  
It doesn't go out-of-bounds with the first step, but since we're starting the wrong side of the maxima, it just goes downhill *away* from the root until it hits the search endpoint.  Which is what I would expect to be honest.  
  
I guess the question is to what lengths we're prepared to go to protect against bad initial guesses?

---

## Comment 4

> Username: NAThompson  
> Created at: 2022-07-27 17:50:19 UTC  
> Url: https://github.com/boostorg/math/issues/808#issuecomment-1197101306  

>  I guess the question is to what lengths we're prepared to go to protect against bad initial guesses?  
  
Yeah, I think the worry is that if you add too many branches for these cases, you either *create* bugs, or create performance issues.  
  
Though maybe "if it goes out the right bound, start the guess at the left" might not be insane?

---

## Comment 5

> Username: wthrowe  
> Created at: 2022-07-28 16:10:54 UTC  
> Url: https://github.com/boostorg/math/issues/808#issuecomment-1198355618  

There are simple robust algorithms for this (see, for example, `rtsafe` from Numerical Recipes), but after converting our project to use one of them I don't think you can switch because it would break too much downstream code.  Since they can fall back to an actual bisection algorithm, they require that the passed bounds are a valid bracket for a bisection search, which is currently not required for your `newton_raphson`.  (Infinite bounds can probably be handled, but I've already hit cases where evaluating `f(bound)` crashes.)
