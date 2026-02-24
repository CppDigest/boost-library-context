# #85 - Tidying [Closed]

> Username: vinniefalco  
> Created at: 2016-09-22 12:37:58 UTC  
> Updated at: 2016-10-20 22:00:10 UTC  
> Closed at: 2016-10-20 22:00:10 UTC  
> Url: https://github.com/boostorg/beast/issues/85  

- Remove boost/system includes (e.g. `<boost/system/error_code.hpp>`) from places that don't need it.  
- Remove unused `headers_type` from `write_preparation` (https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/write.ipp)  
- Use braced-init style construction everywhere (e.g. https://github.com/vinniefalco/Beast/blob/master/include/beast/core/buffer_cat.hpp)  
- Doc for `handler_alloc` should indicate that copies of the handler will be made as needed (https://github.com/vinniefalco/Beast/blob/master/include/beast/core/handler_alloc.hpp)
