# #12 Fixing up some accidental checks that were using #ifdef when instead sho... [Merged]

> Username: stgates  
> Created at: 2014-06-17 19:04:35 UTC  
> Updated at: 2014-06-17 19:08:03 UTC  
> Merged at: 2014-06-17 19:07:23 UTC  
> Closed at: 2014-06-17 19:07:23 UTC  
> Url: https://github.com/boostorg/predef/pull/12  

...uld have been using just #if to define the AVALIABLE macro.  
  
It's easy to get stuck in the habit of checking #ifdef :)

---
