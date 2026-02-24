# #15 use char type directly to avoid invalid aliasing [Merged]

> Username: podusowski  
> Created at: 2018-03-16 10:54:00 UTC  
> Updated at: 2018-04-01 08:32:00 UTC  
> Merged at: 2018-03-31 03:36:03 UTC  
> Closed at: 2018-03-31 03:36:03 UTC  
> Url: https://github.com/boostorg/function/pull/15  

More details: https://svn.boost.org/trac10/ticket/13481  
  
TL;DR:  
Aggregates and unions don't fall into "safe to alias" types, regardless of having `char` as a member which is safe by itself.

---
