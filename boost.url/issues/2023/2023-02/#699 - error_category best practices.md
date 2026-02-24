# #699 - error_category best practices [Closed]

> Username: vinniefalco  
> Created at: 2023-02-28 18:35:53 UTC  
> Updated at: 2023-03-01 00:19:14 UTC  
> Closed at: 2023-03-01 00:19:14 UTC  
> Url: https://github.com/boostorg/url/issues/699  

Each library-defined error_category should also have this member  
  
```  
        char const*  
        message(  
            int ev,  
            char *dest,  
            std::size_t len) const noexcept;  
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-02-28 22:58:57 UTC  
> Url: https://github.com/boostorg/url/issues/699#issuecomment-1449055204  

Also make the category instances `constexpr`.
