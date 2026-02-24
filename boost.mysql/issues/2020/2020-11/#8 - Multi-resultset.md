# #8 - Multi-resultset [Closed]

> Username: anarthal  
> Created at: 2020-11-29 17:01:11 UTC  
> Updated at: 2023-05-24 10:19:09 UTC  
> Closed at: 2023-05-24 10:19:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/8  

Current implementation does not support specifying several queries in a single connection::query(), as this returns multiple resultsets. Support this by adding a mechanism to retrieve several resultsets from a single query.  
Support prepared statements with OUT parameter binding (may also employ this mechanism).
