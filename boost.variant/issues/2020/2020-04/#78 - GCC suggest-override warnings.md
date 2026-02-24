# #78 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-04 17:03:07 UTC  
> Updated at: 2020-05-05 17:47:03 UTC  
> Closed at: 2020-05-05 17:23:34 UTC  
> Url: https://github.com/boostorg/variant/issues/78  

GCC 7.5 fires suggest-override warnings in Boost 1.72:  
  
<pre>  
include/boost/variant/get.hpp:47:26: error: ‘virtual const char* boost::bad_get::what() const’ can be marked override [-Werror=suggest-override]  
include/boost/variant/bad_visit.hpp:31:26: error: ‘virtual const char* boost::bad_visit::what() const’ can be marked override [-Werror=suggest-override]  
</pre>

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-04-09 01:02:09 UTC  
> Updated at: 2020-04-13 00:32:16 UTC  
> Url: https://github.com/boostorg/variant/issues/78#issuecomment-611270470  

Depends on boostorg/config#329.

---

## Comment 2

> Username: apolukhin  
> Created at: 2020-05-05 17:23:50 UTC  
> Url: https://github.com/boostorg/variant/issues/78#issuecomment-624194777  

Thanks for the report!

---

## Comment 3

> Username: EugeneZelenko  
> Created at: 2020-05-05 17:47:03 UTC  
> Url: https://github.com/boostorg/variant/issues/78#issuecomment-624207089  

Thank you for help!
