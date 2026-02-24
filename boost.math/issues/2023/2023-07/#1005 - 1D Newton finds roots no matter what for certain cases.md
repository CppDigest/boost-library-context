# #1005 - 1D Newton finds roots no matter what for certain cases [Open]

> Username: ryanelandt  
> Created at: 2023-07-26 17:24:55 UTC  
> Updated at: 2023-07-26 17:24:55 UTC  
> Url: https://github.com/boostorg/math/issues/1005  

**Summary**  
If all the steps the 1D Newton solver encounters have the same sign, the solver will return a root, no matter what. For example, root finding the function `f(x) = x` over the interval [1, 3] returns a root near 1.  
```  
boost::math::tools::newton_raphson_iterate([](double x) { return std::make_pair(x, 1.0); }, 2.0, 1.0, 3.0, 52);  
```  
**Why this occurs**  
If the 1D Newton root finder overshoots a bound, it will bisect with said bound. Repeated overshoots and subsequent bisections decrease the step size. When the step size is sufficiently small, the convergence criteria is satisfied and a root is returned. This functionality results in answers that are obviously wrong as the above example showed. The behavior of finding roots at ends causes problems with real workflows, e.g., #808.  
  
**Deeper issues**  
A related danger is that people may write code whose apparent correct functioning is a result of the 1D Newton solver's ability to find roots in objective functions that do not contain them. An example of this occurring in Boost Math is the temme root finder which #1004 attempts to correct.
