# #72 - Version 2 of controlled_runge_kutta::try_step for FSAL steppers does not compile [Open]

> Username: reneSchm  
> Created at: 2024-02-27 16:27:51 UTC  
> Updated at: 2024-02-27 16:27:51 UTC  
> Url: https://github.com/boostorg/odeint/issues/72  

Hi!  
I want to use controlled_runge_kutta with runge_kutta_dopri5 and get the output of try_step regardless of its success, but version 2 of try_step does not compile. The error seems to be a missing argument [here](https://github.com/boostorg/odeint/blob/8b4768ef4926051dbe4ffe13782d7540351ff019/include/boost/numeric/odeint/stepper/controlled_runge_kutta.hpp#L685), since in the FSAL version there is no try_step method that takes six arguments.  
  
I think the intention was to call version 4 here, so a possible fix would be to use m_dxdt also as dxdt_out, i.e. change the line (controlled_runge_kutta.hpp:685) to  
```cpp  
return try_step( system , in , m_dxdt.m_v , t , out , m_dxdt.m_v , dt );  
```  
This does compile, and it produces correct results for single steps. However, I did not check if overwriting m_dxdt.m_v has any unwanted side effects for repeated stepping.
