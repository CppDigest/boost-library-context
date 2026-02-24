# #449 - tanh_sinh integral is re-scaled internally but the tolerance isn't [Closed]

> Username: bbbales2  
> Created at: 2020-11-04 16:59:40 UTC  
> Updated at: 2020-11-13 13:21:24 UTC  
> Closed at: 2020-11-13 13:21:24 UTC  
> Url: https://github.com/boostorg/math/issues/449  

The convergence criteria for the tanh_sinh quadratures looks like `error < tol * L1` (https://github.com/boostorg/math/blob/develop/include/boost/math/quadrature/detail/tanh_sinh_detail.hpp#L377).  
  
The problem is that depending on the endpoints of the integral, `error` and `L1` are scaled differently than the actual result. For instance, here's a rescaling if both endpoints are [finite](https://github.com/boostorg/math/blob/develop/include/boost/math/quadrature/tanh_sinh.hpp#L204):  
  
```  
result_type Q = diff*m_imp->integrate(u, error, L1, function, left_min_complement, right_min_complement, tolerance, levels);  
  
if (L1) {  
  *L1 *= diff;  
}  
```  
  
There's a different one [here](https://github.com/boostorg/math/blob/develop/include/boost/math/quadrature/tanh_sinh.hpp#L114).  
  
So whenever I run a quadrature, if I want to check if it is converged, I cannot just double check `error < tol * L1` like the docs say (https://www.boost.org/doc/libs/1_74_0/libs/math/doc/html/math_toolkit/double_exponential/de_levels.html), I gotta check `error < tol * L1 * scaling`, where `scaling` is the internal rescaling of the integrals.  
  
It would be nice if the convergence check was all in the units of the user-facing integral. I don't know what other quadratures this applies to, just hit it on `tanh_sinh`.

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-11-04 18:16:42 UTC  
> Url: https://github.com/boostorg/math/issues/449#issuecomment-721893488  

Just to clarify, do you have an integral whose analytic L1 norm is incorrect?

---

## Comment 2

> Username: bbbales2  
> Created at: 2020-11-04 20:58:47 UTC  
> Url: https://github.com/boostorg/math/issues/449#issuecomment-721971793  

I don't wanna say the analytic L1 norm is incorrect. That sounds like something that would make my integral not exist or something.  
  
What I know is `L1_I1` [here](https://github.com/boostorg/math/blob/develop/include/boost/math/quadrature/detail/tanh_sinh_detail.hpp#L377):  
```  
if (err <= abs(tolerance*L1_I1)) {  
  break;  
}  
```  
  
which is used to judge convergence gets scaled by a constant [here](https://github.com/boostorg/math/blob/develop/include/boost/math/quadrature/tanh_sinh.hpp#L204):  
```  
result_type Q = diff*m_imp->integrate(u, error, L1, function, left_min_complement, right_min_complement, tolerance, levels);  
  
if (L1) {  
  *L1 *= diff;  
}  
```  
  
Which means that when I call the integrator in my code:  
  
```  
boost::math::quadrature::tanh_sinh<double> integrator_right;  
integrator_right.integrate(f_wrap, 0.0, b, tolerance,  
                                       &error, &L, &levels)  
```  
  
It is not necessarily true that `error < tolerance * L` after the function exits, because the thing that is effectively being checked is `error < tolerance * L / (0.5 * b)`. I am doing this check on exit because that is how [this](https://www.boost.org/doc/libs/1_74_0/libs/math/doc/html/math_toolkit/double_exponential/de_levels.html) recommends I check things are converged.  
  
The test case I have is a bit messy since it's buried in generated code for a different library, but I think the way to reproduce this is just use `tanh_sinh` on any finite integral and print `L1_I1` in the detail code and compare it to the `L1` that gets passed back to user-land.  
  
I can multiply my `tolerance` by `0.5 * b` to get the check I expect. Also I clicked your picture and it says Oak Ridge. Hello from East TN!

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-11-04 22:00:48 UTC  
> Url: https://github.com/boostorg/math/issues/449#issuecomment-721998330  

Ok, I think I see the issue. Just to make sure let me repeat it to you:  
  
Our documentation say we check the condition number of integration over the interval provided by the user. But in fact, the termination condition is being checked in the rescaled domain, which has a linearly rescaled condition number of integration.  
  
So in your case, as b -> 0, you are seeing large admissible error.

---

## Comment 4

> Username: bbbales2  
> Created at: 2020-11-04 22:14:09 UTC  
> Url: https://github.com/boostorg/math/issues/449#issuecomment-722003664  

> But in fact, the termination condition is being checked in the rescaled domain  
  
Yes, this is it.  
  
> you are seeing large admissible error  
  
Simpler than that. It's not that we're specifically needing to push b to zero. In the test case I have `b = 0.67`.  
  
The errors we were seeing were things like `failed to integrate, error 9e-10 is not less than tolerance * L1 norm`. I took a test case and checked manually what was happening and `tolerance * L1` is like 8.5e-10, so the thing seems like it's converging, just scaled a bit off so the convergence checks from user code fail.

---

## Comment 5

> Username: bbbales2  
> Created at: 2020-11-04 22:15:35 UTC  
> Url: https://github.com/boostorg/math/issues/449#issuecomment-722004326  

(also in that test case `levels = 4` on return I think, so I think the quadrature is converging happily)

---

## Comment 6

> Username: NAThompson  
> Created at: 2020-11-05 13:56:51 UTC  
> Url: https://github.com/boostorg/math/issues/449#issuecomment-722393867  

Ok, I think I know a fix. If you have a minute, could you post the integrand you hit this with?

---

## Comment 7

> Username: bbbales2  
> Created at: 2020-11-05 14:05:02 UTC  
> Url: https://github.com/boostorg/math/issues/449#issuecomment-722398665  

I think I could clean this up to be just C++/Boost but it will take me a couple days to get around to it.  
  
The test case where this fails right now is kindof a mess. It comes from generated code and has some other sizeable dependencies (my test is [here](https://github.com/stan-dev/math/blob/7f211272bc3557901d8941603de3219fc1112e9e/test/unit/math/prim/functor/integrate_1d_test.cpp#L397) -- it's everything from line 397 down). I'm not sure what the integral is either -- it's something a user gave to me. That will be part of cleaning up the test lol.
