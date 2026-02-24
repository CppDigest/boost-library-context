# #67 - Construct weak_ptr from weak_ptr and pointer [Closed]

> Username: vinniefalco  
> Created at: 2019-04-19 02:00:17 UTC  
> Updated at: 2019-04-22 03:22:54 UTC  
> Closed at: 2019-04-22 03:22:54 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/67  

We'd like this to work  
```  
    return boost::weak_ptr<T>(p->weak_from_this(), p);  
```  
  
This is the workaround:  
```  
    return boost::weak_ptr<T>(  
        boost::static_pointer_cast<T>(  
            p->weak_from_this().lock()));  
```
