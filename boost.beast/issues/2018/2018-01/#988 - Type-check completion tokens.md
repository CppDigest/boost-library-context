# #988 - Type-check completion tokens [Closed]

> Username: vinniefalco  
> Created at: 2018-01-18 13:03:07 UTC  
> Updated at: 2018-02-16 17:46:12 UTC  
> Closed at: 2018-02-16 17:46:12 UTC  
> Url: https://github.com/boostorg/beast/issues/988  

In the update to Asio the completion token type checks are missing, need to add them back. Typical implementation:  
  
```  
static_assert(is_completion_handler<  
        BOOST_ASIO_HANDLER_TYPE(ReadHandler, void(error_code, std::size_t)),  
            void(error_code, std::size_t)>::value,  
            "ReadHandler requirements not met");  
```
