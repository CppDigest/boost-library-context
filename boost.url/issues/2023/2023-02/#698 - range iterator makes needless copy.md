# #698 - range iterator makes needless copy [Closed]

> Username: vinniefalco  
> Created at: 2023-02-28 15:38:50 UTC  
> Updated at: 2023-03-02 14:08:47 UTC  
> Closed at: 2023-03-02 14:08:47 UTC  
> Url: https://github.com/boostorg/url/issues/698  

Here we should probably pass by reference to avoid a costly copy of a variant:  
```  
    bool  
    operator==(  
        iterator other) const noexcept  
```  
  
and other places.
