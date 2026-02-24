# #162 Added BOOST_MAY_ALIAS and BOOST_NO_MAY_ALIAS macros. [Merged]

> Username: Lastique  
> Created at: 2017-07-12 14:55:31 UTC  
> Updated at: 2017-07-21 18:02:03 UTC  
> Merged at: 2017-07-21 17:52:28 UTC  
> Closed at: 2017-07-21 17:52:28 UTC  
> Url: https://github.com/boostorg/config/pull/162  

The macros can be used to mark types that can alias other types (i.e. break C++ strict aliasing rules).  
  
This functionality is used in multiple places throughout Boost, it would be nice to have it in a common place.

---
