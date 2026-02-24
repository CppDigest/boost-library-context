# #589 - Document the need to link to Container in certain situations [Closed]

> Username: grisumbras  
> Created at: 2021-06-26 19:47:21 UTC  
> Updated at: 2023-04-02 18:44:44 UTC  
> Closed at: 2023-04-02 18:44:44 UTC  
> Url: https://github.com/boostorg/json/issues/589  

In particular  
```cxx  
std::vector< json::value, json::polymorphic_allocator < json::value > > v;  
```  
would result in a linking error if built in non-standalone mode.
