# #23 Rename short all-capital typedefs to avoid name clashes with macros. [Merged]

> Username: Lastique  
> Created at: 2017-04-20 12:25:02 UTC  
> Updated at: 2017-04-21 00:17:56 UTC  
> Merged at: 2017-04-21 00:09:08 UTC  
> Closed at: 2017-04-21 00:09:08 UTC  
> Url: https://github.com/boostorg/chrono/pull/23  

In particular, CR is defined in apache2/httpd.h as a character of carriage return (13). CD is also potentially problematic, so rename it as well.

---

## Comment 1

> Username: viboes  
> Created_at: 2017-04-21 00:08:52 UTC  
> Url: https://github.com/boostorg/chrono/pull/23#issuecomment-295979519  

:( I hate unscoped macros.

---
