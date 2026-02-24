# #862 - error_category best practices [Closed]

> Username: vinniefalco  
> Created at: 2023-02-28 18:36:13 UTC  
> Updated at: 2023-03-01 10:02:53 UTC  
> Closed at: 2023-03-01 10:02:53 UTC  
> Url: https://github.com/boostorg/json/issues/862  

Each library-defined error_category should also have this member  
  
```  
        char const*  
        message(  
            int ev,  
            char *dest,  
            std::size_t len) const noexcept;  
  
```
