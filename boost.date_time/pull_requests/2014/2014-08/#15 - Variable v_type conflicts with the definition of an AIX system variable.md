# #15 Variable v_type conflicts with the definition of an AIX system variable. [Closed]

> Username: ismirlian  
> Created at: 2014-08-05 18:41:54 UTC  
> Updated at: 2014-08-08 15:38:19 UTC  
> Closed at: 2014-08-08 15:38:19 UTC  
> Url: https://github.com/boostorg/date_time/pull/15  

This variable conflicts with the definition of an AIX variable and as a result some of the interprocess_example tests fail. By undefining the variable, I free the name so that it can be reused.

---

## Comment 1

> Username: mclow  
> Created_at: 2014-08-06 03:36:12 UTC  
> Url: https://github.com/boostorg/date_time/pull/15#issuecomment-51290152  

I don't like this, mostly because I'm unclear on the ramifications of undefining that macro.  
  
I think it would be better to rename the template parameter from `v_type` to something else like `var_type`. As far as I know, there's nothing special about that name in DateTime

---

## Comment 2

> Username: ismirlian  
> Created_at: 2014-08-08 14:20:06 UTC  
> Url: https://github.com/boostorg/date_time/pull/15#issuecomment-51608069  

My question with that kind of change, is it only in this particular file time_resolution_traits.hpp that I have to change the name or are there other places in tests that I have to change the name?

---
