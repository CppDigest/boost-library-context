# #57 - value_type cannot have an explicit contructor [Open]

> Username: rabraker  
> Created at: 2022-12-03 00:53:31 UTC  
> Updated at: 2022-12-03 00:53:31 UTC  
> Url: https://github.com/boostorg/odeint/issues/57  

There are many places in odeint similar to the following (taken from `runge_kutta_dopri5.hpp`):  
  
```  
        stepper_base_type::m_algebra.for_each3( m_x_tmp.m_v , in , dxdt_in ,  
                typename operations_type::template scale_sum2< value_type , time_type >( 1.0 , dt*b21 ) );  
  
```  
  
Unfortunately, if `value_type`'s constructor is marked `explicit`, this will fail. I ran into this trying to use a [ceres a jet type](https://github.com/ceres-solver/ceres-solver/blob/master/include/ceres/jet.h#L223) for the scalars here for the purpose of automatic differentiation.  
  
It seems like it would be more robust to use something like  
```  
const value_type one{1.0};  
```  
in these scenarios instead of the raw `1.0`. And if I make that change, the ceres jet type works correctly.   
  
I would be willing to work on a PR if this is a change the maintainers would accept.
