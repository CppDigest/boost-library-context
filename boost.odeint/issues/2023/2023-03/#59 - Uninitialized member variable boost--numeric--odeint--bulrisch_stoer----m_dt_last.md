# #59 - Uninitialized member variable boost::numeric::odeint::bulrisch_stoer<>::m_dt_last [Open]

> Username: HHildenbrandt  
> Created at: 2023-03-08 15:15:43 UTC  
> Updated at: 2023-03-08 15:15:43 UTC  
> Url: https://github.com/boostorg/odeint/issues/59  

It seems that `m_dt_last` is used w/o initialization in the first call to `try_check`:  
  
```  
// boots/numeric/odeint/stepper/bulrisch_stoer.hpp  
206        if( dt != m_dt_last )    // <-- m_dt_last indeterminate?  
207        {  
208          reset(); // step size changed from outside -> reset  
209        }  
```
