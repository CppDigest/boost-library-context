# #73 - `static_cast<time_type>` in `integrate_times` [Open]

> Username: mjziebarth  
> Created at: 2024-04-06 11:34:17 UTC  
> Updated at: 2024-04-06 11:34:17 UTC  
> Url: https://github.com/boostorg/odeint/issues/73  

Hi,  
  
it seems to me that there are a number of `static_cast<time_type>` in the `integrate_times` implementation, e.g.  
https://github.com/boostorg/odeint/blob/8b4768ef4926051dbe4ffe13782d7540351ff019/include/boost/numeric/odeint/integrate/detail/integrate_times.hpp#L64  
https://github.com/boostorg/odeint/blob/8b4768ef4926051dbe4ffe13782d7540351ff019/include/boost/numeric/odeint/integrate/detail/integrate_times.hpp#L97  
https://github.com/boostorg/odeint/blob/8b4768ef4926051dbe4ffe13782d7540351ff019/include/boost/numeric/odeint/integrate/detail/integrate_times.hpp#L139  
(and potentially more; L139 is where I encountered a compiler error) that don't fit the the target variables' types. Say, for instance, `Time` in these cases is a Boost Units quantity, then `time_type`, if I understand correctly, is a plain double (or other floating point format). If I further understand the `TimeIterator` type correctly, that type should return a `Time` instance on valid dereference.  
  
Now the `static_cast` tries to cast a quantity to, say, double (which fails), which is then reassigned to a quantity (which should  
also fail).  
  
It seems to me that simply removing the static casts might fix the issue, although I have not spent much time looking into it.  
For now I'll use another integrator but I'd be happy to help to solve this issue in the future - if my hypothesis is correct.  
  
Best  
Malte
