# #823 - Use aligned_union in detail::variant [Closed]

> Username: vinniefalco  
> Created at: 2017-10-21 14:01:31 UTC  
> Updated at: 2017-10-22 17:53:39 UTC  
> Closed at: 2017-10-22 17:53:39 UTC  
> Url: https://github.com/boostorg/beast/issues/823  

```  
typename std::aligned_union<1, TN...>::type buf_;  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-22 17:53:39 UTC  
> Url: https://github.com/boostorg/beast/issues/823#issuecomment-338496150  

Unfortunately glibc++ 4.8 doesn't have `aligned_union` so this cannot be done.
