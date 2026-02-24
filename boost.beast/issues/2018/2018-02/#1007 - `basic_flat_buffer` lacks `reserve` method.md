# #1007 - `basic_flat_buffer` lacks `reserve` method [Closed]

> Username: cmazakas  
> Created at: 2018-02-06 17:29:34 UTC  
> Updated at: 2019-01-13 03:00:15 UTC  
> Closed at: 2019-01-13 03:00:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1007  

```  
    return (std::min<std::size_t>)(  
        (std::max<std::size_t>)(512, buffer.capacity() - size),  
        (std::min<std::size_t>)(max_size, limit));  
```  
  
Buffer capacity is used to decide how much to read by `detail::read_some_op`. As a feature request, it'd be useful to be able to control the capacity of `basic_flat_buffer`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-01-13 03:00:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1007#issuecomment-453797810  

This should be in the master branch now
