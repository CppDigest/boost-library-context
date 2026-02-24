# #11 Removed extra semicolon [Merged]

> Username: Kojoley  
> Created at: 2018-09-26 12:28:42 UTC  
> Updated at: 2018-09-26 13:00:03 UTC  
> Merged at: 2018-09-26 12:58:03 UTC  
> Closed at: 2018-09-26 12:58:03 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/11  

```  
In file included from ../../../../boost/throw_exception.hpp:36:  
../../../../boost/exception/exception.hpp:15:57: error: extra ';' outside of a function is a C++11 extension [-Werror,-Wc++11-extra-semi]  
namespace boost { template <class T> class shared_ptr; };  
                                                        ^  
1 error generated.  
```

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-09-26 12:57:52 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/11#issuecomment-424705293  

You held that in reserve? :-)

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-09-26 12:59:59 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/11#issuecomment-424705906  

Nope :)  
For unknown reason it recently appeared in Spirit CI without any change.

---
