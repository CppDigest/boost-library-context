# #427 Normalize static/dynamic link macros and avoid redefinition warnings [Merged]

> Username: pdimov  
> Created at: 2023-12-22 03:41:30 UTC  
> Updated at: 2023-12-22 13:14:53 UTC  
> Merged at: 2023-12-22 13:14:53 UTC  
> Closed at: 2023-12-22 13:14:53 UTC  
> Url: https://github.com/boostorg/python/pull/427  

The static/dynamic config macro logic had two issues; first, it used unconventional macro names (BOOST_PYTHON_STATIC_LIB and BOOST_PYTHON_DYNAMIC_LIB instead of BOOST_PYTHON_STATIC_LINK and BOOST_PYTHON_DYNAMIC_LINK as everything else); second, when both BOOST_PYTHON_STATIC_LINK and BOOST_PYTHON_STATIC_LIB were defined, it caused a macro redefinition warning.  
  
This change fixes both.

---
