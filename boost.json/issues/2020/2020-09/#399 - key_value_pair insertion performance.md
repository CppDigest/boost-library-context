# #399 - key_value_pair insertion performance [Closed]

> Username: vinniefalco  
> Created at: 2020-09-22 17:25:07 UTC  
> Updated at: 2020-10-10 02:18:24 UTC  
> Closed at: 2020-10-10 02:18:24 UTC  
> Url: https://github.com/boostorg/json/issues/399  

Seems slow:  
```  
o.insert( boost::json::key_value_pair( sv, std::move( w ) ) );  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-10-10 02:18:24 UTC  
> Url: https://github.com/boostorg/json/issues/399#issuecomment-706470045  

This should be much better now, previously `key_value_pair` was missing a move constructor.
