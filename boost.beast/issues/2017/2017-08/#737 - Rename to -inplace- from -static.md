# #737 - Rename to "inplace" from "static" [Closed]

> Username: vinniefalco  
> Created at: 2017-08-13 23:57:01 UTC  
> Updated at: 2017-09-05 01:00:19 UTC  
> Closed at: 2017-09-05 00:59:57 UTC  
> Url: https://github.com/boostorg/beast/issues/737  

```  
multi_buffer            basic_multi_buffer  
inplace_buffer          inplace_buffer_base  
flat_buffer             basic_flat_buffer  
flat_inplace_buffer     flat_inplace_buffer_base  
  
inplace_string  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-05 00:59:57 UTC  
> Updated at: 2017-09-05 01:00:19 UTC  
> Url: https://github.com/boostorg/beast/issues/737#issuecomment-327045404  

Boost.Container uses the word "static" (e.g. `static_vector`) to mean the same thing, so we should leave this as-is.
