# #69 remove dependence on now empty boost_system library [Merged]

> Username: JeffGarland  
> Created at: 2020-07-06 21:11:45 UTC  
> Updated at: 2020-07-07 23:40:48 UTC  
> Merged at: 2020-07-07 23:40:48 UTC  
> Closed at: 2020-07-07 23:40:48 UTC  
> Url: https://github.com/boostorg/random/pull/69  

I found in boost 1.73 boost.random had a requirement to build boost_system even though system is now all header.  This change removes the dependence on building boost_system.  Checked against g++9 on Linux -- rebuilt library, tests and examples.

---
