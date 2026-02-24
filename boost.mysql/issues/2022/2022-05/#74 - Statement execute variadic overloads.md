# #74 - Statement execute variadic overloads [Closed]

> Username: anarthal  
> Created at: 2022-05-12 18:25:49 UTC  
> Updated at: 2022-11-28 07:57:51 UTC  
> Closed at: 2022-11-28 07:57:51 UTC  
> Url: https://github.com/boostorg/mysql/issues/74  

Support  
  
```  
statement.execute("hello", "world", 42);  
statement.async_execute("hello", "world", 42, use_future);  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2022-11-28 07:57:51 UTC  
> Url: https://github.com/boostorg/mysql/issues/74#issuecomment-1328675677  

Causes issues for async functions, especially when default completion tokens are used. As of d04f2662ba161710f4602f9406884f15e9d3f4b3, a new tuple-like syntax is available that should be almost equivalent to this.
