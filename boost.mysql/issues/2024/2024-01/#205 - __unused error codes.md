# #205 - __unused error codes [Closed]

> Username: anarthal  
> Created at: 2024-01-03 11:33:13 UTC  
> Updated at: 2024-01-14 13:14:29 UTC  
> Closed at: 2024-01-14 13:14:29 UTC  
> Url: https://github.com/boostorg/mysql/issues/205  

There are a couple of error codes, like `er_no_such_partition__unused`, that contain a double underscore. That's illegal in C++. Since they reference no longer used error codes, they can probably just be removed.
