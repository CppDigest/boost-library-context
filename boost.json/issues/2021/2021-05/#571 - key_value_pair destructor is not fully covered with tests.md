# #571 - key_value_pair destructor is not fully covered with tests [Open]

> Username: grisumbras  
> Created at: 2021-05-22 12:46:04 UTC  
> Updated at: 2021-05-22 12:46:04 UTC  
> Url: https://github.com/boostorg/json/issues/571  

The branch  
```  
if(sp.is_not_shared_and_deallocate_is_trivial())  
    return;  
```  
is never tested.
