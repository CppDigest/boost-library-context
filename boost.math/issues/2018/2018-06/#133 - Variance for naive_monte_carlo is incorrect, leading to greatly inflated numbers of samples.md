# #133 - Variance for naive_monte_carlo is incorrect, leading to greatly inflated numbers of samples [Closed]

> Username: jzmaddock  
> Created at: 2018-06-28 12:15:42 UTC  
> Updated at: 2018-06-29 13:01:05 UTC  
> Closed at: 2018-06-29 08:07:33 UTC  
> Url: https://github.com/boostorg/math/issues/133  

I don't see the bug off hand, so this is probably one for @NAThompson .  
  
If I test `naive_monte_carlo` with a simple Pi calculator:  
  
```  
   auto g = [](std::vector<double> const & x)->double  
   {  
      double r = x[0] * x[0] + x[1] * x[1];  
      if (r <= 1)  
      {  
         return 1;  
      }  
      return 0;  
   };  
```  
  
Then `naive_monte_carlo::variance()` reports ~2.7, but I believe the correct variance is 0.16, this can be checked with something like:  
  
```  
      using namespace boost::accumulators;  
      boost::random::taus88 t(123455677);  
      accumulator_set<double, stats<tag::variance> > acc1;  
  
      for (unsigned i = 0; i < count; ++i)  
      {  
         double x, y;  
         x = boost::random::uniform_real_distribution<double>(-1, 1)(t);  
         y = boost::random::uniform_real_distribution<double>(-1, 1)(t);  
         double val = x * x + y * y <= 1 ? 1 : 0;  
         acc1(val);  
      }  
  
      std::cout << "Mean = " << boost::accumulators::mean(acc1) << std::endl;  
      std::cout << "Variance = " << variance(acc1) << std::endl;  
```  
  
As a consequence, if you specify an error bound of say 1e-3, you get an error estimate of 8e-4, and an actual error of 4e-5.  In other words there is some gross over-sampling going on here.  
  
Found while testing/comparing to CUDA version which calculates the variance differently.

---

## Comment 1

> Username: NAThompson  
> Created at: 2018-06-29 04:52:15 UTC  
> Updated at: 2018-06-29 04:53:47 UTC  
> Url: https://github.com/boostorg/math/issues/133#issuecomment-401246946  

Ok, I'm still chewing on this, but here's my current thoughts: Let $f$ be the integrand which we wish to integrate over a volume $V$. We are computing the variance of the samples of the function $g$, where $g$ is defined via $g(u_0, u_1, \ldots) = Vf(x_{0, min} + k_0 u_0, x_{1,min} + k_1 u_1, \ldots)$ on the domain $[0,1]^d$.  
  
Now, since $Var(aX) = a^2 Var(X)$, then your belief is that we should be returning the variance $Var(f) = Var(g)/V^2$, which is exactly the discrepancy you observe: 2.7/4^2 = .16, roughly.  
  
First, this difference does not lead to oversampling, since the error estimate is the same in both case: $sqrt(Var(g)/N) = V*sqrt(Var(f)/N)$.  
  
Next, it is not clear to me what the proper definition of the variance is in the presence of the variable transforms. Certainly for unbounded domains $V*sqrt(Var(f)/N)$ is not defined. For bounded domains, things are a bit better, but not unambiguous. For instance, suppose we use the integral relation  
  
Var(g) = \int_{[0,1]^d} (g(u) - I)^2 du  
  
where  
  
I := \int_{[0,1]^d} g(u)  du = \int_{V} f(x)  dx = V\<f\>  
  
If we wish to preserve the relationship Var(g) = V^2Var(f), then the only integral identify for Var(f) is  
  
Var(f) = \int_{[0,,1]^d} (f(x_i+k_i u_i) - \<f\>)^2  du  
   
But I don't think this is really what people expect. I think they would rather think about  
  
J := \int_{V} (f(x) - \<f\>)^2 dx  
  
which satisfies Var(g) = V*J, rather than a quadratic relation. In any case, for the finite volume I'm not opposed to changing the variance by a factor of 1/V^2, but for the infinite volume case I'm flummoxed.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-06-29 08:07:33 UTC  
> Url: https://github.com/boostorg/math/issues/133#issuecomment-401281347  

My bad: you're quite right, variance should relate to the scaled result that the user gets back, not the samples over [0,1] we use internally.  I'm so used to dealing with relative errors I'd forgotten this was absolute, and user expectation would be that their absolute bound is relative to the mean scaled by the volume (ie the return value).  I need to rethink this - sorry for the noise!

---

## Comment 3

> Username: NAThompson  
> Created at: 2018-06-29 13:01:05 UTC  
> Url: https://github.com/boostorg/math/issues/133#issuecomment-401347286  

This is definitely not noise; I noticed that this is not documented. I'm in quickbook math symbol hell trying to get something legible written!
