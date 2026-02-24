# #755 - Invalid iterator used in reporter.hpp [Closed]

> Username: bachase  
> Created at: 2017-08-29 14:17:38 UTC  
> Updated at: 2017-08-29 17:14:03 UTC  
> Closed at: 2017-08-29 17:14:03 UTC  
> Url: https://github.com/boostorg/beast/issues/755  

In the unit test reporter   
https://github.com/boostorg/beast/blob/develop/test/extras/include/boost/beast/unit_test/reporter.hpp#L173-L175, `iter` may be invalidated by the `resize` call if it points to the last entry in `top`. The subsequent `emplace` call is incorrectly using the invalid iterator.    
  
```  
if(iter != top.end())  
{  
    if(top.size() == max_top)  
        top.resize(top.size() - 1);  
    top.emplace(iter, r.name, elapsed);  
}  
```  
I first noticed this using VS2017.

---

## Comment 1

> Username: bachase  
> Created at: 2017-08-29 14:48:23 UTC  
> Url: https://github.com/boostorg/beast/issues/755#issuecomment-325688200  

Looks good. Thanks!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-08-29 14:49:12 UTC  
> Url: https://github.com/boostorg/beast/issues/755#issuecomment-325688463  

```  
        if(iter != top.end())  
        {  
            top.emplace(iter, r.name, elapsed);  
            if(top.size() > max_top)  
                top.resize(max_top);  
        }  
```
