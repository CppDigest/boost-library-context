# #9 Fix if compiler misses support for rvalue-ref and/or variadic templates. [Merged]

> Username: DenizThatMenace  
> Created at: 2016-06-22 09:12:58 UTC  
> Updated at: 2017-03-24 13:59:18 UTC  
> Merged at: 2017-03-24 13:59:18 UTC  
> Closed at: 2017-03-24 13:59:18 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/9  

Compilation of _Boost.TypeErasure_ fails if macro `BOOST_NO_CXX11_RVALUE_REFERENCES` and/or macro `BOOST_NO_CXX11_VARIADIC_TEMPLATES` are defined.  
  
This pull-request fixes that, so that compilation of _Boost.TypeErasure_ succeeds wheter one or both of these macros are defined or not.  
  
The associated trac-ticket is: [12291](https://svn.boost.org/trac/boost/ticket/12291)

---
