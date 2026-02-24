# #459 - Don't link to container [Closed]

> Username: vinniefalco  
> Created at: 2020-10-25 16:40:29 UTC  
> Updated at: 2021-06-26 19:47:43 UTC  
> Closed at: 2021-06-26 19:47:43 UTC  
> Url: https://github.com/boostorg/json/issues/459  

Now that Boost.Container has the fix for DLL export, we can remove the need to link to it in the Jamfile.

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-06-23 07:24:01 UTC  
> Url: https://github.com/boostorg/json/issues/459#issuecomment-866597291  

I don't think that's true. `polymorphic_allocator`'s default constructor uses a non-inline function from Container. E.g.  
```  
std::vector< value, json::polymorphic_allocator < json::value > > v;  
```  
will result in a linking error.
