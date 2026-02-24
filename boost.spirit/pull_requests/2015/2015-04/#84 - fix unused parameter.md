# #84 fix unused parameter [Merged]

> Username: tabe  
> Created at: 2015-04-29 03:37:57 UTC  
> Updated at: 2015-06-03 15:24:11 UTC  
> Merged at: 2015-06-03 15:24:11 UTC  
> Closed at: 2015-06-03 15:24:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/84  

This avoids a warning like:  
  
```  
/some/path/include/boost/spirit/home/lex/lexer/lexertl/functor_data.hpp:171:42: warning: unused parameter ‘it’ [-Wunused-parameter]  
             void set_end(Iterator const& it) {}  
                                          ^  
```

---

## Comment 1

> Username: djowel  
> Created_at: 2015-04-29 03:39:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/84#issuecomment-97295768  

please comment it out instead, like: /_iter_/. it's our convention.

---
