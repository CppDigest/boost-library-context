# #2646 - error_category best practices [Closed]

> Username: vinniefalco  
> Created at: 2023-02-28 18:36:29 UTC  
> Updated at: 2024-06-06 05:33:32 UTC  
> Closed at: 2024-01-03 14:51:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2646  

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

> Username: klemens-morgenstern  
> Created at: 2023-03-06 00:06:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2646#issuecomment-1455256065  

Since we're on it, can I make the categories public?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2023-03-06 00:15:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2646#issuecomment-1455260340  

> can I make the categories public?  
  
No that's not necessary
