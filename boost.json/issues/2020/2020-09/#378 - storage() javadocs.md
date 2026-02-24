# #378 - storage() javadocs [Closed]

> Username: vinniefalco  
> Created at: 2020-09-22 16:56:37 UTC  
> Updated at: 2020-09-22 16:59:29 UTC  
> Closed at: 2020-09-22 16:59:29 UTC  
> Url: https://github.com/boostorg/json/issues/378  

value::storage() should say: Return a reference to the storage_ptr to the memory_resource associated with the value. The same thing applies to a few other classes.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-22 16:59:29 UTC  
> Url: https://github.com/boostorg/json/issues/378#issuecomment-696850600  

The current javadocs are fine:  
```  
This returns a pointer to the memory resource that was used to construct the value.  
```
