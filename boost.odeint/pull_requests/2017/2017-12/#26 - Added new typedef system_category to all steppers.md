# #26 Added new typedef system_category to all steppers [Closed]

> Username: friedrichatgc  
> Created at: 2017-12-28 17:04:25 UTC  
> Updated at: 2018-01-05 20:26:07 UTC  
> Closed at: 2018-01-05 20:26:07 UTC  
> Url: https://github.com/boostorg/odeint/pull/26  

This new typedef must be one of  
- explicit_system_tag  
- second_order_system_tag  
- symplectic_system_tag  
- simple_symplectic_system_tag  
- symplectic_or_simple_symplectic_system_tag  
- implicit_system_tag  
and defines the type of the system the stepper handles.  
This can be usefull to automatically provide a proper system to a  
stepper using template spezialization.

---

## Comment 1

> Username: friedrichatgc  
> Created_at: 2018-01-05 20:26:06 UTC  
> Url: https://github.com/boostorg/odeint/pull/26#issuecomment-355656719  

moving PRs to headmyshoulder/odeint-v2

---
