# #605 - use boost::make_unique_no_init [Closed]

> Username: vinniefalco  
> Created at: 2017-07-06 02:31:46 UTC  
> Updated at: 2017-07-11 00:06:46 UTC  
> Closed at: 2017-07-11 00:06:46 UTC  
> Url: https://github.com/boostorg/beast/issues/605  

> Various default-initialization new[] could be boost::make_unique_noinit:  
```  
    p_ = boost::make_unique_noinit<std::uint8_t[]>(capacity_);  
    rd_.buf = boost::make_unique_noinit<std::uint8_t[]>(rd_.buf_size);  
```
