# #107 - error_info::message may contain user-supplied input [Closed]

> Username: anarthal  
> Created at: 2023-01-17 18:39:18 UTC  
> Updated at: 2023-01-17 21:46:09 UTC  
> Closed at: 2023-01-17 21:46:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/107  

Currently `error_info::message` is being treated as trusted, but it should not. It is not guaranteed to be UTF8-encoded, and may contain user-supplied values.
