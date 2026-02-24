# #99 Fix argument value selection with an rvalue default [Merged]

> Username: Lastique  
> Created at: 2020-03-15 15:21:12 UTC  
> Updated at: 2020-03-23 15:29:15 UTC  
> Merged at: 2020-03-23 15:07:25 UTC  
> Closed at: 2020-03-23 15:07:25 UTC  
> Url: https://github.com/boostorg/parameter/pull/99  

In C++11 mode, when named parameter pack was a single tagged argument, parameter value was not extracted when an rvalue default value was provided by the user (instead, the default value was returned). This commit adds a missing overload for `default_r_`, which returns the parameter  
value.  
  
Fixes https://github.com/boostorg/parameter/issues/97.

---
