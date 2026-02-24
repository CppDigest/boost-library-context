# #7 Fix broken echo -n syntax on Macs [Merged]

> Username: Belcourt  
> Created at: 2014-01-25 06:08:15 UTC  
> Updated at: 2014-06-18 07:01:22 UTC  
> Merged at: 2014-01-27 22:11:31 UTC  
> Closed at: 2014-01-27 22:11:31 UTC  
> Url: https://github.com/boostorg/boost/pull/7  

Avoid using shell builtin echo command on Darwin as built-in echo doesn't support -n option.  Use standalone echo executable instead that does support -n option, normally located in /bin/echo, if it exists.

---
