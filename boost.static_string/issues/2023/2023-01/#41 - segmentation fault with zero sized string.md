# #41 - segmentation fault with zero sized string [Closed]

> Username: ivan-volnov  
> Created at: 2023-01-11 14:05:10 UTC  
> Updated at: 2023-01-19 01:09:47 UTC  
> Closed at: 2023-01-19 01:09:47 UTC  
> Url: https://github.com/boostorg/static_string/issues/41  

Hello. Thank you for the great library!  
  
Found an error.  
  
To reproduce:  
  
```cpp  
    boost::static_strings::static_string<0> a;  
    auto b = a;  
```

---

## Comment 1

> Username: alandefreitas  
> Created at: 2023-01-11 20:52:06 UTC  
> Url: https://github.com/boostorg/static_string/issues/41#issuecomment-1379470245  

Thanks, Ivan! I'll have a look.
