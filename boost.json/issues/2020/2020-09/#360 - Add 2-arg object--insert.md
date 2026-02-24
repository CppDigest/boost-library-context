# #360 - Add 2-arg object::insert [Closed]

> Username: vinniefalco  
> Created at: 2020-09-18 15:54:24 UTC  
> Updated at: 2020-10-28 00:04:35 UTC  
> Closed at: 2020-10-28 00:04:35 UTC  
> Url: https://github.com/boostorg/json/issues/360  

```  
template< class M >  
std::pair<iterator, bool>  
object::  
insert( string_view, M&& m );  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-10-28 00:04:35 UTC  
> Url: https://github.com/boostorg/json/issues/360#issuecomment-717611846  

We already have  
```  
    template<class Arg>  
    std::pair<iterator, bool>  
    emplace(string_view key, Arg&& arg);  
```  
  
so I don't think this is needed.
