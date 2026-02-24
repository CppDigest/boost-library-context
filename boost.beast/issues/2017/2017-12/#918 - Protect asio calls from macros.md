# #918 - Protect asio calls from macros [Closed]

> Username: vinniefalco  
> Created at: 2017-12-02 12:25:42 UTC  
> Updated at: 2017-12-09 22:25:58 UTC  
> Closed at: 2017-12-09 22:25:58 UTC  
> Url: https://github.com/boostorg/beast/issues/918  

Calls to `get_associated_executor` and `get_associated_allocator` need the function in parenthesis to protect from using macro invocations
