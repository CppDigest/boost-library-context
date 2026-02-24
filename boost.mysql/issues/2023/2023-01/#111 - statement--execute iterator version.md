# #111 - statement::execute iterator version [Closed]

> Username: anarthal  
> Created at: 2023-01-26 12:32:19 UTC  
> Updated at: 2023-05-24 10:19:10 UTC  
> Closed at: 2023-05-24 10:19:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/111  

Currently, if you need to use iterators, you must use `statement::start_execution` + `connection::read_some_rows`. Provide a `statement::execute` overload accepting an iterator range.
