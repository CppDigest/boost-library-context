# #44 - Retrieve last-used `dt` value from controlled stepper? [Open]

> Username: blsqr  
> Created at: 2020-10-29 16:01:17 UTC  
> Updated at: 2020-10-29 16:01:17 UTC  
> Url: https://github.com/boostorg/odeint/issues/44  

I was wondering if it is possible to retrieve the last-used `dt` value when using `integrate_adaptive` with a controlled stepper?  
Use case: Calling `integrate_adaptive` multiple times in succession, i.e. continuing from the time the previous integration stopped at. I want to avoid starting with a `dt` that is too small or too large for the successive call to `integrate_adaptive`.  
  
What I mean with "last-used `dt`" is the last _adaptively chosen_ `dt`, i.e. ignoring the `dt` that was chosen to end up at `t1`; see [boost docs](https://www.boost.org/doc/libs/1_74_0/libs/numeric/odeint/doc/html/boost_numeric_odeint/odeint_in_detail/integrate_functions.html):  
  
> If stepper is a Controlled Stepper then `dt` is the initial step size. The actual step size is changed according to error control of the stepper. For the last step, the step size will be reduced to ensure we end exactly at `t1`. If provided, the observer is called after each time step (and before the first step at `t0`).  
  
I guess it would be possible to do this via the observer? However, I feel like it would be valuable if it was possible to retrieve it some other way... Is this information stored anywhere or is it overwritten when choosing the `dt` value to end up at `t1`?    
Digging into the code I only found [`ode_iterator_base::m_dt`](https://github.com/boostorg/odeint/blob/d94c80da6a6ac4f975b6dda71d4bc7644a33b9c5/include/boost/numeric/odeint/iterator/detail/ode_iterator_base.hpp#L109), of which I'm not at all sure that it is the relevant value.  
  
Any guidance on this issue would be greatly appreciated. Thanks :)
