# #495 - basic_fields::set optimization [Closed]

> Username: vinniefalco  
> Created at: 2017-06-15 15:03:07 UTC  
> Updated at: 2017-06-15 19:49:21 UTC  
> Closed at: 2017-06-15 19:49:20 UTC  
> Url: https://github.com/boostorg/beast/issues/495  

`set` should use the iterator from the initial call to find to also perform the erases and inserts
