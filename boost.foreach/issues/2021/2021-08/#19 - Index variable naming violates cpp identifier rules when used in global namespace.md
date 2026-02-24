# #19 - Index variable naming violates cpp identifier rules when used in global namespace [Open]

> Username: thebaron06  
> Created at: 2021-08-04 12:39:53 UTC  
> Updated at: 2021-08-09 10:43:29 UTC  
> Url: https://github.com/boostorg/foreach/issues/19  

The naming of the index variables (eg. [here](https://github.com/boostorg/foreach/blob/develop/include/boost/foreach.hpp#L1107)) violates the identifier naming rules as can be found [here](https://en.cppreference.com/w/cpp/language/identifiers) when used in the global namespace, ie. free global functions.  
All of them are starting with a leading underscore (_).  
This is problematic when implementing custom checks using clang's libtooling (macros are already replaced).
