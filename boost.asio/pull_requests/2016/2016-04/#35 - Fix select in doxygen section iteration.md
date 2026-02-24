# #35 Fix select in doxygen section iteration [Closed]

> Username: vinniefalco  
> Created at: 2016-04-20 09:02:03 UTC  
> Updated at: 2021-10-11 18:28:31 UTC  
> Closed at: 2021-10-11 18:28:31 UTC  
> Url: https://github.com/boostorg/asio/pull/35  

This solves a problem where the xsl transformation sees either the typedefs, or the classes and functions at namespace scope, but not both, when generating reference.qbk.

---
