# #398 - Consolidate error_type [Closed]

> Username: vinniefalco  
> Created at: 2022-08-13 14:01:43 UTC  
> Updated at: 2022-08-16 18:57:04 UTC  
> Closed at: 2022-08-16 18:57:04 UTC  
> Url: https://github.com/boostorg/url/issues/398  

Move all error related aliases into `namespace error_type`, header "error_type.hpp", lift this into the `boost::urls` namespace. Move `result` here as well, get rid of "result.hpp". We have to think about this one.
