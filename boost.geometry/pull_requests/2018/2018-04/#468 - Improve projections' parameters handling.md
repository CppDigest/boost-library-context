# #468 Improve projections' parameters handling. [Merged]

> Username: awulkiew  
> Created at: 2018-04-07 13:06:27 UTC  
> Updated at: 2018-05-10 16:01:51 UTC  
> Merged at: 2018-05-10 16:01:51 UTC  
> Closed at: 2018-05-10 16:01:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/468  

This PR makes the same changes as closed https://github.com/boostorg/geometry/pull/461 but it's implemented on top of https://github.com/boostorg/geometry/pull/465.  
  
In short it allows to replace this:  
  
    pj_param(pvalues, "sdatum").s; //old  
  
with this:  
  
    pj_get_param_s(pvalues, "datum"); //new  
  
and this:  
  
    //old  
    if (pj_param(parameters, "tR").i)  
        a = pj_param(parameters, "dR").f;  
  
with this:  
  
    //new  
    pj_param_f(parameters, "R", a);  
  
So the information about the parameter type is expressed in compile-time as function name instead of passed as character in cstring. But most importantly the function doesn't have to be called twice to check if the parameter is set and then get it.

---

## Comment 1

> Username: vissarion  
> Created_at: 2018-04-20 08:13:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/468#issuecomment-383019809  

Looks ok to me, thanks!

---
