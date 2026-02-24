# #74 - Extra ; warnings [Open]

> Username: iljah  
> Created at: 2024-04-10 11:41:41 UTC  
> Updated at: 2024-04-10 11:41:41 UTC  
> Url: https://github.com/boostorg/odeint/issues/74  

At least these files produce extra ; warnings with GCC 13 and -Wpedantic in boost 1.84:  
boost/numeric/odeint/stepper/detail/adaptive_adams_bashforth_moulton.hpp  
boost/numeric/odeint/stepper/detail/adaptive_adams_coefficients.hpp  
boost/numeric/odeint/stepper/detail/pid_step_adjuster.hpp  
boost/numeric/odeint/stepper/detail/controlled_adams_bashforth_moulton.hpp  
  
Example:  
```  
boost/numeric/odeint/stepper/adaptive_adams_bashforth_moulton.hpp:99:6: varoitus: ylimääräinen ”;” [-Wpedantic]  
   99 |     };  
      |      ^  
      |      -  
```
