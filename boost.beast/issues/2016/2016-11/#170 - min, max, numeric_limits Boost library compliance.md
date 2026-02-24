# #170 - min, max, numeric_limits Boost library compliance [Closed]

> Username: vinniefalco  
> Created at: 2016-11-07 11:27:28 UTC  
> Updated at: 2016-11-12 17:42:46 UTC  
> Closed at: 2016-11-12 17:42:46 UTC  
> Url: https://github.com/boostorg/beast/issues/170  

_"Make sure your code compiles in the presence of the min() and max() macros. Some platform headers define min() and max() macros which cause some common C++ constructs to fail to compile. Some simple tricks can protect your code from inappropriate macro substitution:"_  
http://www.boost.org/development/requirements.html
