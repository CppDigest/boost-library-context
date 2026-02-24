# #27 Fixed dense_output_runge_kutta<Stepper, stepper_tag>::do_step(...) [Closed]

> Username: friedrichatgc  
> Created at: 2017-12-29 15:36:03 UTC  
> Updated at: 2018-01-05 20:27:23 UTC  
> Closed at: 2018-01-05 20:27:23 UTC  
> Url: https://github.com/boostorg/odeint/pull/27  

do_step must return the pair (t, t+dt) but (t, dt) is returned.  
  
Note that dense_output_runge_kutta<Stepper, explicit_controlled_stepper_fsal_tag>::do_step(...)  
works correctly.

---

## Comment 1

> Username: friedrichatgc  
> Created_at: 2018-01-05 20:27:23 UTC  
> Url: https://github.com/boostorg/odeint/pull/27#issuecomment-355656998  

moving PRs to headmyshoulder/odeint-v2

---
