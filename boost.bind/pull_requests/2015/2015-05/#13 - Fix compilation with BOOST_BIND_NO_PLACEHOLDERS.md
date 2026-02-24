# #13 Fix compilation with BOOST_BIND_NO_PLACEHOLDERS [Merged]

> Username: MarcelRaad  
> Created at: 2015-05-29 09:31:51 UTC  
> Updated at: 2015-09-11 09:18:57 UTC  
> Merged at: 2015-05-29 20:50:44 UTC  
> Closed at: 2015-05-29 20:50:45 UTC  
> Url: https://github.com/boostorg/bind/pull/13  

If BOOST_BIND_NO_PLACEHOLDERS is defined, there is no namespace boost::placeholders.  
  
The errors are visible in most of the odeint tests:  
http://www.boost.org/development/tests/develop/developer/numeric-odeint.html

---
